# Tokenomics

SafidoSwap's sustainability relies on a deflationary tokenomics model and controlled incentive distribution.

## SAFI Token

SAFI is the governance and utility token. It has a finite supply, eliminating infinite inflation risks.

* **Max Supply:** 100,000,000 (100 Million) SAFI.
* **Contract Address:** Defined in `VITE_TOKEN`.

### Initial Distribution
* **95% (95,000,000 SAFI):** Allocated to **SAFIVault**. This establishes a community-first distribution.
* **5% (5,000,000 SAFI):** Allocated to the **Owner** (Bootstrapping, Partnerships, Dev Treasury).

## The Vault & Emission

`SAFIVault` dictates monetary policy through an exponential decay model.

The emission is governed by `stakeBps` (Staking Basis Points), currently configured to **1 bp (0.01%)**. Every day (24 hours), the vault releases 0.01% of its remaining balance.

This conservative rate creates a highly sustainable emission curve (decaying exponential), ensuring the reward pool lasts for decades while keeping inflation minimal.

**Simulation:**
* **Day 1:** Balance = 95,000,000 SAFI.
    * Emission: $95,000,000 \times 0.0001 = 9,500$ SAFI.
* **Day 2:** Balance = 94,990,500 SAFI.
    * Emission: $94,990,500 \times 0.0001 = 9,499$ SAFI.

## Keeper System & Security

The Vault's monetary policy requires a daily external trigger via the public `run()` function.

* **Decentralized Execution:** Unlike centralized cron jobs, SafidoSwap incentivizes any user to trigger the `run()` function by offering a reward in SAFI tokens.
* **Verification Architecture:** The protocol utilizes an off-chain "Watcher" node that validates execution. When a user triggers the Vault:
    1.  The API verifies the transaction receipt on the Pharos blockchain.
    2.  It confirms the interaction was strictly with the Emissions Vault (`ADDR.VAULT`) and executed the correct selector (`run()`).
    3.  Once verified, the `VaultReward` contract is authorized to credit the caller (the `tx.from` address) as the "Winner of the Day".

**Security:** This "Verify-on-Chain" logic prevents spoofing. Even if a malicious actor sends a fake transaction hash to the API, the system rejects it upon cross-referencing with the blockchain state.