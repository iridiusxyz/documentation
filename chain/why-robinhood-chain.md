---
description: The features of Robinhood Chain that make it the right foundation for Iridius.
---

# Choosing Robinhood Chain

The short version: on Robinhood Chain, RWAs are plain ERC-20s that users self-custody, issued by a regulated entity and priced through Chainlink feeds. An oracle-anchored venue needs precisely that mix, and today no other chain offers it, still less alongside a broker's own distribution.

## Each property in turn

The details below are drawn from Robinhood's documentation and from the Arbitrum Foundation.

| Property | Robinhood Chain | Why it matters to Iridius |
| --- | --- | --- |
| Stack | Its own Arbitrum Nitro (Orbit) chain running in rollup mode, settling to Ethereum with blob data availability and a 7-day canonical withdrawal | Inherits Ethereum security and works with the usual Arbitrum tooling |
| Status | Mainnet live since 1 July 2026, at about $540M TVL by mid-August 2026 (estimates run from $536M to $1.4B) | Genuine assets and genuine users are present today |
| Deployment | Permissionless and EVM-equivalent, supporting Solidity and Vyper, Foundry and Hardhat, plus Stylus for Rust and WASM | No gatekeeper sits between the venue's contracts and the chain |
| Block time and fees | Roughly 250 ms blocks, roughly 100 ms latency via preconfirmations, a median transaction of about $0.001, and ETH as the gas token | Quotes a few seconds old are still fresh, and sub-cent settlement makes small swaps viable |
| Sequencer | A single first-come-first-served sequencer run by Robinhood, which screens sanctions at that layer, with forced inclusion via the L1 delayed inbox after 24 hours | Plan around a 24-hour outage; the Chainlink Sequencer Uptime Feed can be used |
| Native RWAs | **Stock Tokens**: 18-decimal ERC-20s issued by Robinhood Assets (Jersey) Ltd, each backed 1:1 by shares in US custody, offered in more than 120 countries | The set of assets the venue trades, from a listed broker |
| Corporate actions | ERC-8056 `uiMultiplier()`, through which dividends are reinvested and splits appear as shares per token | No dividend bookkeeping and no ex-dividend cliff to mishandle |
| Oracles | Chainlink Data Feeds at 24/5, Data Streams on the v11 RWA schema with `marketStatus`, CCIP and the Sequencer Uptime Feed, all with the multiplier built into the price | Production-quality anchor pricing and session awareness from launch |
| Stablecoins | Paxos's USDG, native and MiCA-regulated, already serving as the lending dollar on Morpho; USDe is live; USDC via CCTP remains *unconfirmed* | The quote asset |
| Account abstraction | ERC-4337 EntryPoints v0.6 to v0.8 and EIP-7702 on ArbOS 40, with Alchemy Gas Manager and ZeroDev | Gasless onboarding, with approval and swap bundled into a single user operation |
| Incumbent DeFi | Morpho Blue, Uniswap v2, v3 and v4, Lighter, Arcus, 1inch | Arbitrageurs and aggregator flow are already on the chain |
| Compliance layer | **None on-chain** for Stock Tokens: no allowlist and no transfer hook; eligibility is handled in Robinhood's interface and in KYC'd primary issuance and redemption | Iridius has to enforce eligibility itself, at the protocol boundary |
| Ecosystem economics | Robinhood remits 10% of chain net revenue to the Arbitrum DAO and put $1M into sponsoring Arbitrum Open House 2026 | Builders have access to grants and audit subsidies |

## What other chains lack today

* On Solana, tokenized equities (the xStocks Kraken distributes) trade largely on centralised books and generic AMMs, and they are issued by third parties, not by the broker that owns the users.
* Coinbase brought tokenized equities to Base on 24 August 2026, so recently that nobody has yet built a venue layer on top.
* Ethereum mainnet has the deepest DeFi anywhere, yet broker-issued equities are not native there and gas costs rule out small RWA swaps.

Only on Robinhood Chain do the assets, the pricing, the quote asset, account abstraction and the fee profile all come together, and there the issuing broker contributes its own distribution as well.

## Gaps the chain leaves for us to fill

Making the case honestly means naming the gaps too:

* Nothing on-chain enforces Stock Token eligibility. Iridius handles that with the [eligibility registry](../architecture/eligibility.md).
* There is no built-in protection against sequencer outages. Iridius adds it with the recovery grace and the delayed inbox route. See [Sequencer and chain risk](../risk/sequencer-and-chain.md).
* Gaps while markets are closed are left unpriced. Iridius prices them with the regime engine. See [Trading regimes](../protocol/trading-regimes.md).
* The existing DEXs handle an RWA as if it were any other token, and closing that gap is why the venue exists. See [Why now](../introduction/why-now.md).
