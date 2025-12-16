# Developer Reference

## Network Details

SafidoSwap operates on the Pharos Atlantic Testnet.

| Parameter | Value |
| :--- | :--- |
| **Network Name** | Pharos Atlantic |
| **Chain ID** | `688689` |
| **Currency Symbol** | PHRS |
| **RPC URL** | `https://atlantic.dplabs-internal.com` |
| **Block Explorer** | [atlantic.pharosscan.xyz](https://atlantic.pharosscan.xyz) |

---

## Contract Addresses

These are the official deployment addresses for the current testnet version.

| Contract | Variable Name | Address | Description |
| :--- | :--- | :--- | :--- |
| **Router** | `VITE_ROUTER` | `0xa499eC96cb0b9B5f4A1B74580662250fa282c3F5` | Main entry point for trading and adding liquidity. |
| **Factory** | `VITE_FACTORY` | `0x7280511d40e06A3333FB48B0a59A6E8F0005112E` | Deploys new Pair contracts via `create2`. |
| **SAFI Token** | `VITE_TOKEN` | `0x4436C5E183E6B3AD64a83F6d0A82a030D81c08cF` | The governance and utility token. |
| **Staking** | `VITE_STAKING` | `0xD2d59de1b101BAd8deaac2f79EE12838b5d1E0B6` | Manages yield farming and reward distribution. |
| **Vault** | `VITE_VAULT` | `0xD569E7ECc9D671e87A84d45A7be0D944083631E0` | Controls emission logic and monetary policy. |
| **Lottery** | `VITE_SAFILUCK` | `0x95178114C69fc06ac399ADD6ce789eCeE378320a` | The "SafidoPrize" luck engine. |

---

## Integration ABIs

Below are the essential JSON ABIs required to interact with the core protocol features.

### 1. Router (Swapping & Liquidity)
*Functions: `swapExactTokensForTokens`, `addLiquidity`, `removeLiquidity`, `getAmountsOut`*

```json
[
  {
    "inputs": [
      {"internalType": "uint256", "name": "amountIn", "type": "uint256"},
      {"internalType": "uint256", "name": "amountOutMin", "type": "uint256"},
      {"internalType": "address[]", "name": "path", "type": "address[]"},
      {"internalType": "address", "name": "to", "type": "address"},
      {"internalType": "uint256", "name": "deadline", "type": "uint256"}
    ],
    "name": "swapExactTokensForTokens",
    "outputs": [{"internalType": "uint256[]", "name": "amounts", "type": "uint256[]"}],
    "stateMutability": "nonpayable",
    "type": "function"
  },
  {
    "inputs": [
      {"internalType": "address", "name": "tokenA", "type": "address"},
      {"internalType": "address", "name": "tokenB", "type": "address"},
      {"internalType": "uint256", "name": "amountADesired", "type": "uint256"},
      {"internalType": "uint256", "name": "amountBDesired", "type": "uint256"},
      {"internalType": "uint256", "name": "amountAMin", "type": "uint256"},
      {"internalType": "uint256", "name": "amountBMin", "type": "uint256"},
      {"internalType": "address", "name": "to", "type": "address"},
      {"internalType": "uint256", "name": "deadline", "type": "uint256"}
    ],
    "name": "addLiquidity",
    "outputs": [],
    "stateMutability": "nonpayable",
    "type": "function"
  },
  {
    "inputs": [
      {"internalType": "address", "name": "tokenA", "type": "address"},
      {"internalType": "address", "name": "tokenB", "type": "address"},
      {"internalType": "uint256", "name": "liquidity", "type": "uint256"},
      {"internalType": "uint256", "name": "amountAMin", "type": "uint256"},
      {"internalType": "uint256", "name": "amountBMin", "type": "uint256"},
      {"internalType": "address", "name": "to", "type": "address"},
      {"internalType": "uint256", "name": "deadline", "type": "uint256"}
    ],
    "name": "removeLiquidity",
    "outputs": [],
    "stateMutability": "nonpayable",
    "type": "function"
  },
  {
    "inputs": [
      {"internalType": "uint256", "name": "amountIn", "type": "uint256"},
      {"internalType": "address[]", "name": "path", "type": "address[]"}
    ],
    "name": "getAmountsOut",
    "outputs": [{"internalType": "uint256[]", "name": "amounts", "type": "uint256[]"}],
    "stateMutability": "view",
    "type": "function"
  }
]
```

### 2. Factory (Pool Creation)
*Functions: `createPair`*

```json
[
  {
    "inputs": [
      {"internalType": "address", "name": "tokenA", "type": "address"},
      {"internalType": "address", "name": "tokenB", "type": "address"}
    ],
    "name": "createPair",
    "outputs": [{"internalType": "address", "name": "pair", "type": "address"}],
    "stateMutability": "nonpayable",
    "type": "function"
  }
]
```

### 3. Staking (Yield Farming)
*Functions: `stake`, `unstake`, `claim`, `claimAndStake`, `pendingRewards`*

```json
[
  {
    "inputs": [{"internalType": "uint256", "name": "amount", "type": "uint256"}],
    "name": "stake",
    "outputs": [],
    "stateMutability": "nonpayable",
    "type": "function"
  },
  {
    "inputs": [{"internalType": "uint256", "name": "amount", "type": "uint256"}],
    "name": "unstake",
    "outputs": [],
    "stateMutability": "nonpayable",
    "type": "function"
  },
  {
    "inputs": [],
    "name": "claim",
    "outputs": [],
    "stateMutability": "nonpayable",
    "type": "function"
  },
  {
    "inputs": [],
    "name": "claimAndStake",
    "outputs": [],
    "stateMutability": "nonpayable",
    "type": "function"
  },
  {
    "inputs": [{"internalType": "address", "name": "account", "type": "address"}],
    "name": "pendingRewards",
    "outputs": [{"internalType": "uint256", "name": "", "type": "uint256"}],
    "stateMutability": "view",
    "type": "function"
  }
]
```

### 4. Lottery (SafidoPrize)
*Functions: `buyTickets`, `claim`, `currentRoundId`*

```json
[
  {
    "inputs": [{"internalType": "uint32", "name": "amount", "type": "uint32"}],
    "name": "buyTickets",
    "outputs": [],
    "stateMutability": "nonpayable",
    "type": "function"
  },
  {
    "inputs": [{"internalType": "uint64", "name": "roundId", "type": "uint64"}],
    "name": "claim",
    "outputs": [],
    "stateMutability": "nonpayable",
    "type": "function"
  },
  {
    "inputs": [],
    "name": "currentRoundId",
    "outputs": [{"internalType": "uint64", "name": "", "type": "uint64"}],
    "stateMutability": "view",
    "type": "function"
  }
]
```