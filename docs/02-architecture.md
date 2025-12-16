# Core Architecture

The system is composed of three interconnected pillars, the "Safido Trinity":

1.  **The Exchange:** An optimized Uniswap V2 fork that manages liquidity, executes swaps, and collects fees. Composed of Factory, Router, and Pair contracts.
2.  **The Vault:** The protocol's central bank. The `SAFIVault` contract controls the emission of the SAFI governance token based on a time-decay curve rather than fixed inflation.
3.  **The Luck Engine:** The `SAFILuck` contract consumes protocol fees and user tokens to redistribute wealth through a verifiable on-chain raffle, creating buy pressure and burn mechanisms for the native token.

## Automated Market Maker (AMM)

At the heart of SafidoSwap lies an Automated Market Maker (AMM) enabling decentralized ERC-20 token swaps. Unlike traditional order books, the AMM uses liquidity pools and a constant product formula to determine prices.

The protocol adheres to the fundamental invariant:

$$x \times y = k$$

Where:
* $x$ is the quantity of token A in the pool.
* $y$ is the quantity of token B in the pool.
* $k$ is a constant that must not decrease during trades.

When a user swaps amount $\Delta x$ of token A for token B, the new quantity of token B ($y'$) is calculated such that the invariant holds (pre-fees). The user receives $\Delta y = y - y'$.

## Fee Structure

A significant innovation over standard AMMs is SafidoSwap's flexible, granular fee structure. While standard protocols often fix fees at 0.30%, SafidoSwap implements a competitive and configurable model found in `Factory.sol`.

The current total fee charged on every swap is **0.15% (15 basis points)**. This structure ensures competitive pricing while sustaining rewards for liquidity providers and the protocol's lottery system.

The distribution is governed by state variables in the Factory:

| Fee Component | Value (bps) | Nominal % | Destination | Technical Description |
| :--- | :--- | :--- | :--- | :--- |
| **Total Swap Fee** | 15 bps | 0.15% | N/A | Total cost incurred by the trader. |
| **LP Fee** | 10 bps | 0.10% | Liquidity Providers | Added to pool reserves, compounding LP token value. |
| **Protocol Fee** | 5 bps | 0.05% | Protocol Vault | Sent directly to a protocol vault (Treasury/Lottery). |

### Router-Based Fee Architecture

Unlike standard AMMs that fix fees within the liquidity pair contract, SafidoSwap utilizes a flexible Router-based fee logic.

* **Protocol Fee Execution:** The protocol fee (0.05%) is extracted by the Router **before** the swap occurs and sent directly to the Lottery Vault. This prevents swap logic errors and ensures the lottery is funded instantly.
* **LP Fee Execution:** The Liquidity Provider fee (0.10%) is applied mathematically during the swap calculation, ensuring reserves grow with every trade.
* **Global Configuration:** Both fee tiers are managed globally via the Factory contract, allowing for network-wide adjustments without needing to migrate liquidity pairs.

### Contract Mechanics

#### Factory (`Factory.sol`)
Responsible for deploying new liquidity pairs using the `create2` opcode. This allows deterministic address generation, enabling the Router and frontend to calculate a pair's address (e.g., SAFI/WPHRS) off-chain.

#### Pair (`Pair.sol`)
Holds the actual liquidity assets and calculates the swap math based on the Constant Product Formula ($x \times y = k$).

* **Reserve Management:** Stores `reserve0` and `reserve1` using `uint112`. This packing optimization allows both reserves and the block timestamp (`uint32`) to fit in a single 256-bit storage slot, significantly saving gas during updates.
* **Direct Fee Routing:** Unlike Uniswap V2, which complicates the pair contract by minting LP tokens for protocol fees, SafidoSwap handles the protocol fee via the Router before the assets reach the pair.

**Example Scenario:** A user swaps 10,000 USDC for SAFI.
1.  **Total Fee (0.15%):** 15 USDC total cost to the user.
2.  **Protocol Share (0.05%):** 5 USDC are extracted by the Router and sent immediately to the `protocolFeeVault` (Lottery).
3.  **LP Share (0.10%):** 10 USDC are implicitly retained in the pool reserves during the swap calculation.

This architecture ensures the lottery is funded with liquid assets instantly, without needing to sell LP tokens or perform costly withdrawals from the pair.

#### Router (`Router.sol`)
The peripheral interaction layer responsible for user-facing operations.

* **Asset Handling:** Automatically handles WPHRS/PHRS wrapping and unwrapping, allowing users to trade native coins seamlessly.
* **Slippage Protection:** Enforces the `amountOutMin` parameter. If the executed price is worse than this user-defined limit, the transaction reverts with a "SLIPPAGE" error to prevent value loss.
* **Fee Routing:** Uniquely executes the protocol fee extraction before the swap logic, routing funds directly to the Lottery Vault.

This architecture simplifies the Pair contract, leading to lower overall transaction costs (gas efficiency) compared to fee models that rely on LP token manipulation.

## Security: Anti-Reentrancy

The protocol implements a defense-in-depth strategy to prevent reentrancy attacks across all modules that manage assets.

* **Protected Modules:** All funds-handling contracts—specifically Liquidity Pairs, Staking, SafiLuck (Lottery), and the Faucet—are secured using mutex locks (e.g., OpenZeppelin's `nonReentrant` modifier or the custom lock mechanism in Pairs).
* **Mechanism:** This architecture strictly enforces the Checks-Effects-Interactions pattern. By locking the contract state during execution, the system ensures that internal balances and status flags (such as the Faucet's `lastClaim` timestamp or the Lottery's `claimed` boolean) are updated before any external value transfer is finalized.
* **Attack Prevention:** This prevents recursive exploits where a malicious contract attempts to re-enter the protocol (e.g., calling claim multiple times) to drain funds before the ledger is updated.