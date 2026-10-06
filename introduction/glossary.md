---
description: All the terms used across this documentation, defined together on one page.
---

# Glossary

| Term | Definition |
| --- | --- |
| **Anchor vault** | A per-asset contract that holds inventory in both the Stock Token and USDG and quotes both sides around the oracle mid. See [Anchor vaults](../protocol/anchor-vaults.md). |
| **Attestation** | A record on the Ethereum Attestation Service that ties a wallet to a role (trader, LP, maker) once off-chain verification is complete. It holds no personal data. See [Eligibility registry](../architecture/eligibility.md). |
| **Band** | The maximum distance from the oracle mid within which a fill must fall. A fill outside it causes the transaction to revert. |
| **Clip** | The most a single swap is allowed to draw from a vault. Larger orders are routed to RFQ for a quote. |
| **Data Feeds** | Chainlink's on-chain price service based on pushed updates; its 24/5 equity feeds supply Stock Token prices across the week. |
| **Data Streams** | Chainlink's low-latency service based on pulled reports (v11 RWA schema). Each report carries a `marketStatus` field, and session classification reads from it. |
| **ERC-8056 multiplier** | The value `uiMultiplier()` returns on a Stock Token, giving the underlying shares held per token. Dividends and splits are reflected by changing it, and Chainlink prices already factor it in. |
| **Execution quality** | The signed distance, in basis points, between a fill and the oracle mid at the moment of inclusion. Published for every fill. See [Execution quality](../transparency/execution-quality.md). |
| **Half-spread** | The gap between the mid and one side of the quote. Every Iridius parameter is expressed as a half-spread in basis points. |
| **Inventory skew** | The amount by which a vault's holdings have drifted from its target mix. Quoting leans in response, so flow that brings the mix back gets the better price. |
| **Maker** | An attested market maker that signs RFQ quotes off-chain, with its fills settling atomically on-chain. |
| **Mid** | An asset's guarded oracle price, as reported by `OracleRouter`. Without a mid there is no quote. |
| **Quote asset** | USDG, the unit every market is priced in and every fee is charged in. |
| **Regime** | The state a market is in at any moment (`OPEN`, `EXTENDED`, `CLOSED` or `HALTED`), which determines its spread multiplier and clip. See [Trading regimes](../protocol/trading-regimes.md). |
| **RFQ** | Request for quote: maker-signed EIP-712 messages that remain valid for a few seconds and are settled atomically by the taker. See [RFQ settlement](../protocol/rfq.md). |
| **Router** | `SwapRouter`, the one entry point: it checks eligibility, compares the vault price with the RFQ price, and settles whichever is better. |
| **Session** | The oracle-reported state of the underlying market: regular hours, extended hours or closed. Regimes are built on top of it. |
| **Stock Token** | An equity tokenized by Robinhood Assets (Jersey) Ltd, structured as an ERC-20 debt security backed 1:1 by shares held in US custody. See [Stock Tokens](../assets/stock-tokens.md). |
| **Tier** | The listing class (A, B or C) assigned to an asset, which sets its base spread, clip, band and caps. See [Listing framework](../assets/listing-framework.md). |
| **USDG** | Paxos Global Dollar, a stablecoin regulated under MiCA and issued natively on Robinhood Chain. See [Quote asset: USDG](../assets/usdg.md). |
| **Vault share** | The ERC-4626-style token an LP receives on deposit, representing a pro rata claim on the vault's inventory and on the spread it has accrued. |
