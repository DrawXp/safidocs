# Products

## Staking (Yield Farming)

Holders can earn passive yield by staking SAFI to capture Vault emissions.

The system uses the scalable "Reward per Token" algorithm ($O(1)$ complexity).

$$
RPT_{new} = RPT_{old} + \frac{TotalStaked}{Reward_{notified} \times 10^{18}}
$$

**User pending yield:**

$$
Pending = Balance_{user} \times (RPT_{current} - RPT_{user\_paid})
$$

* **Claim & Stake (Compound):** A critical convenience function allowing users to claim rewards and immediately redeposit them, maximizing APY through frequent compounding.
* **APY Calculation:** The `aprWatcher` service (`index.ts`) calculates real-time APY by monitoring the current `SAFIVault` emission rate, SAFI price vs PHRS (via `getReserves`), and total staked tokens.

## SafidoPrize (Lottery)

SafidoPrize (`SAFILuck.sol`) is the ecosystem's entertainment and deflation engine. Leveraging Pharos's speed, rounds occur every 8 hours (`roundDurationSec = 28800`).

### Mechanism
1.  **Open:** Users call `buyTickets`.
2.  **Seal & Commit:** Once the round ends, the Keeper seals the state and submits a hash (`commitHash`) of a secret random number.
3.  **Reveal & Draw:** The Keeper reveals the secret. Final randomness is derived from this secret combined with the block hash of the seal block.
    * `Seed = keccak256(abi.encode(blockhash, secret, roundId))`
    * *This Commit-Reveal scheme prevents miners/validators from manipulating block hashes to win.*
4.  **Claim Window:** Upon winner selection, a countdown starts. The winner has exactly 7 hours and 50 minutes (`claimWindowSec = 28200`) to execute the `claim()` transaction.
5.  **Rollover:** If the prize is not claimed within this window, the funds are automatically moved to the `carryPool` for the next round via `rolloverIfExpired`, increasing the future jackpot ("Snowball" jackpots).

**Fees:** A Treasury Fee (currently 1%) is removed for protocol maintenance/burn. The Net Pot is distributed to the winner.

## Onboarding Faucet

A regulated mechanism for new users to acquire initial SAFI tokens for gas and testing.

* **Sybil Resistance:** Reads the `lastClaim` timestamp from the smart contract to enforce the `COOLDOWN` period.
* **Visual Feedback:** Displays a dynamic progress bar indicating exactly when the next claim is available, improving the user experience for newcomers.