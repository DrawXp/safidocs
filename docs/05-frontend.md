# Frontend Architecture

The React application (`App.tsx`) serves as the gateway to the protocol, featuring a modular architecture where each functional domain operates as a self-contained unit.

The **Home** interface acts as a visual hub, directing users via interactive cards to the core modules: `/swap`, `/pool`, `/stake`, `/safidoprize`, and `/faucet`.

## Swap Module

The swap module implements a fully decentralized, serverless routing engine directly in the user's browser. Instead of relying on an opaque backend API, the client verifies and calculates all routes and prices locally.

* **Graph Construction & Discovery:** Upon initialization, the client retrieves the full list of active pairs (cached via `localStorage` or fetched from the Indexer) and constructs an in-memory adjacency graph. This ensures the dApp functions even if the backend is intermittent.
* **Algorithmic Pathfinding (BFS):** The engine executes a Breadth-First Search (BFS) algorithm to find the optimal route, prioritizing direct swaps for gas efficiency but searching for 2-hop paths (e.g., Token A → WPHRS → Token B) to bridge disjointed liquidity pools.
* **Real-Time On-Chain Quoting:** Once a path is identified, the client utilizes `useReadContracts` (wagmi) to perform a multicall fetch of the exact reserves. Prices are calculated locally using the Constant Product Formula.
* **Just-In-Time (JIT) Protection:** To prevent failures due to price movements, the UI forces a fresh refetch of on-chain reserves immediately before signature request. If the re-calculated price deviates significantly from the user's quote, the process halts.
* **Native Asset Abstraction:** The router detects interactions involving native PHRS and handles the wrapping (deposit) and unwrapping (withdraw) of WPHRS transparently within the transaction flow.

## Liquidity Management

* **Hybrid Caching:** Implements a snapshot system (`dexPairsSnapshot` in local storage) to load pairs instantly, reducing RPC load.
* **Token Discovery:** Automatically resolves symbols and decimals for unknown tokens found in new pairs.
* **Pair Creation Wizard:** Users can deploy new liquidity pools directly from the UI. The interface prevents "PHRS-PHRS" errors and handles the initial liquidity seeding in a single flow.

## Yield & Keeper Dashboard

* **Real-Time APY:** Calculates the user's APR dynamically based on the ratio between `previewStakeDaily` (emission) and `totalStaked`.
* **Gamified Maintenance:** The UI exposes the `run()` function to users when the epoch ends. It includes a tooltip explaining that triggering the distribution generates claimable rewards, effectively decentralizing the protocol's maintenance.
* **Auto-Refresh Strategy:** Uses visibility listeners (`visibilitychange`) and block watchers to refresh balances immediately after window focus or new blocks, ensuring data is never stale.

## Lottery Interface

* **Winner Verification:** Automatically checks if the connected wallet won the previous round. It displays a distinct status: "You are the winner," "Already claimed," or "Deadline passed."
* **Extra Prize Discovery:** Queries `viewRoundExtras` to list any additional tokens (ERC20) added to the pot besides the main jackpot.
* **Runner Rewards:** Integrates with the `VaultReward` hook to allow users to claim participation rewards (from triggering the Keeper system) directly on the lottery page.

## Transaction Safety Features

* **Slippage Protection:** Calculates `amountOutMin` locally based on user tolerance (default 0.5% - 1%) before transaction submission.
* **Transaction Simulation:** Uses `publicClient.simulateContract` to pre-validate every action (Swap, Stake, Claim). This catches errors like "Insufficient Balance" or "Transfer Failed" before the user pays gas fees.
* **Block Confirmation Watchers:** Custom `waitByBlocks` logic polls the RPC for transaction receipts, ensuring the UI (balances/allowances) updates only after the blockchain has confirmed the state change.