---
description: Reference information on Robinhood Chain.
---

# Network details

{% hint style="info" %}
Robinhood publishes RPC endpoints, explorer URLs and its own contract addresses at [docs.robinhood.com/chain](https://docs.robinhood.com/chain/). Iridius addresses are kept in `contracts/deployments/4663.json` and described on the [Deployment](../architecture/deployment.md) page. Verify every address against those sources before sending anything to it.
{% endhint %}

## Network

| Network         | Chain ID | Status                    | Iridius               |
| --------------- | -------- | ------------------------- | ---------------------- |
| Robinhood Chain | 4663     | Mainnet since 1 July 2026 | Production deployment  |

## Chain parameters

| Parameter                | Value                                                                                  |
| ------------------------ | -------------------------------------------------------------------------------------- |
| Execution                | Arbitrum Nitro, EVM-equivalent, running ArbOS 40 with support for EIP-7702             |
| Settlement layer         | Ethereum                                                                               |
| Data availability        | Ethereum blobs (rollup mode)                                                           |
| Block time               | About 250 ms                                                                           |
| Preconfirmation latency  | About 100 ms                                                                           |
| Gas token                | ETH                                                                                    |
| Median transaction cost  | About $0.001                                                                           |
| Canonical withdrawal     | 7 days                                                                                 |
| Sequencer                | One sequencer, run by Robinhood, which screens for sanctions at that layer             |
| Forced inclusion         | Via the L1 delayed inbox once 24 hours have passed                                     |
| Explorer                 | Blockscout                                                                             |

## Which precompiles and conventions Iridius uses

| Item                      | Usage                                                                                   |
| ------------------------- | --------------------------------------------------------------------------------------- |
| `block.timestamp`         | All time-based logic: quote expiries, staleness limits, grace periods and the timelock   |
| `block.number`            | Not used for timing, because Arbitrum derives it from the L1 block                       |
| `ArbSys.arbBlockNumber()` | Kept for the occasional case that needs the true L2 block height, for example event metadata |
| Compressed calldata       | Because L1 data cost makes up most of the fee, RFQ quote structs are packed and signatures compacted |

## Standards adopted

| Standard   | Where                                                                                   |
| ---------- | --------------------------------------------------------------------------------------- |
| ERC-20     | Stock Tokens use 18 decimals and USDG uses 6                                            |
| ERC-8056   | Stock Token corporate actions, via `uiMultiplier()`                                     |
| ERC-4626   | Accounting for vault shares, extended for dual assets and in-kind withdrawal            |
| EIP-712    | Signatures on RFQ quotes                                                                |
| EIP-1271   | Verifying maker signatures produced by smart accounts                                   |
| ERC-4337   | Account abstraction entry points, v0.6 to v0.8                                          |
| EIP-7702   | Letting an EOA delegate to smart-account code                                           |
| Permit2    | Allowance-based transfers pulled at settlement from both makers and takers              |
| EAS        | Storage for eligibility attestations                                                    |

## Chainlink services on Robinhood Chain

| Service                    | Purpose in Iridius                                    |
| -------------------------- | ------------------------------------------------------ |
| Data Feeds (24/5 equities) | Supplies the mid for vault quoting and band checks     |
| Data Streams (v11 RWA)     | Classifying sessions and cross-checking fills          |
| Sequencer Uptime Feed      | Post-outage grace period                               |
| CCIP                       | Bridging for treasuries and gold once they are listed  |
| ACE / CCID                 | An alternative eligibility adapter                     |
