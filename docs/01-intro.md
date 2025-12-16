---
slug: /
sidebar_position: 1
---

# Introduction

SafidoSwap is a next-generation decentralized finance (DeFi) protocol built natively on the Pharos Atlantic Testnet. Distinct from traditional decentralized exchanges (DEXs) that focus solely on asset swapping, SafidoSwap introduces an integrated, gamified financial ecosystem.

Our architecture combines a high-performance Automated Market Maker (AMM), a deflationary algorithmic emission system, and a provably fair on-chain lottery, all powered by the sub-second finality of the Pharos network.

This documentation serves as the definitive guide to the protocol, covering everything from core smart contract mechanics to UI nuances and the mathematical equations governing the SAFI token economy. Designed for developers, auditors, and advanced users, this report dissects every component with technical precision.

## Mission

Our mission is to transcend passive liquidity. Through the motto **"Trade the waves, crave the gains"**, the protocol incentivizes active participation where traders, liquidity providers, and Keepers collaborate to maintain an efficient, secure, and perpetually incentivized market.

## Network Configuration

To understand the capabilities of SafidoSwap, one must first understand the substrate it operates on. The choice of the Pharos Network is fundamental to the viability of our high-frequency products, such as the SafidoPrize.

SafidoSwap is deployed on the **Pharos Atlantic Testnet**, a Layer 1 blockchain compatible with the Ethereum Virtual Machine (EVM), optimized for Real World Assets (RWA) and cross-chain liquidity.

Correct protocol operation relies strictly on the following network settings:

| Parameter | Technical Configuration | Protocol Implication |
| :--- | :--- | :--- |
| **Network Name** | Pharos Atlantic | Visual identification in wallets. |
| **Chain ID** | `688689` | Unique identifier for signing transactions (EIP-155), preventing replay attacks. |
| **Native Currency** | Pharos (PHRS) | Used for gas payments and wrapped as WPHRS for liquidity pairs. |
| **Decimals** | 18 | ERC20 standard for mathematical precision. |
| **Primary RPC** | `https://atlantic.dplabs-internal.com` | Entry point for state reading and transaction submission. |
| **Block Explorer** | `https://atlantic.pharosscan.xyz` | Verification and transparency tool for all on-chain operations. |

Powered by the Pharos network's sub-second finality, SafidoSwap delivers a seamless user experience. Our deflationary algorithmic emission system works in tandem with a provably fair on-chain lottery that executes rounds every 8 hours. This architecture ensures immediate transaction confirmation for ticket purchases and rewards, eliminating the lag and high costs often faced on mainnet legacy chains.

### Official Resources

To interact with the protocol, users must bridge assets or claim testnet tokens (PHRS).

* **Network Documentation:** [docs.pharosnetwork.xyz](https://docs.pharosnetwork.xyz)
* **Testnet Portal:** [testnet.pharosnetwork.xyz](https://testnet.pharosnetwork.xyz)
* **Official Quests:** [testnet.pharosnetwork.xyz/experience](https://testnet.pharosnetwork.xyz/experience)

#### Faucets
Users can claim testnet PHRS from the following sources to pay for gas fees on SafidoSwap:

| Source | Type | URL |
| :--- | :--- | :--- |
| **Pharos Atlantic** | Official | [testnet.pharosnetwork.xyz](https://testnet.pharosnetwork.xyz) |
| **ZAN** | Partner | [zan.top/faucet/pharos](https://zan.top/faucet/pharos) |
| **Zentra** | Ecosystem | [app.zentrafi.xyz/faucet](https://app.zentrafi.xyz/faucet) |
| **Faroswap** | Ecosystem | [faroswap.xyz/faucet](https://faroswap.xyz/faucet) |
| **Topnod** | Marketing | [topnod.com/marketing/pharos](https://topnod.com/marketing/pharos) |