---
description: Where the venue's revenue comes from.
---

# Business model

Iridius earns a small, itemised portion of the value it provides: fees on fills and a share of the spread the vaults collect. That is the complete list. There are no emissions, no payment for order flow, no trading against users and no selling of data.

## Revenue lines

| Line | Rate (initial) | Scales with |
| --- | --- | --- |
| Vault swap fee | 2 bps of notional | Volume |
| RFQ settlement fee | 2 bps of notional | Block volume |
| Vault spread share | 10% of realised spread | Volume × spread, so it earns the most exactly when LPs do |

Each of the three is a timelocked `ParamController` value, shown on every ticket and recorded in every fill event. See [Fees](../protocol/fees.md).

## The arithmetic

With 10M USDG in average daily volume and an average all-in half-spread of 12 bps, fees bring in about 730K USDG a year and the spread share about 438K, for roughly 1.2M USDG in total each year. Run the same numbers at 50M daily volume and the result is about 5.8M a year. For context, on-chain tokenized-equity trading across all venues came to around $9B in the first eight months of 2026, so the model only needs a modest share of a market growing by several hundred percent annually.

## Sources of volume

* **A built-in pricing advantage on RWAs.** Because anchored quotes do not drift and do not hand LP value to arbitrage, the venue can quote tighter spreads than generic pools for the same LP return. When aggregators route, the better quote wins on merit.
* **The only venue open around the clock that prices the session.** Weekend and overnight demand is real, since tokens on the chain never stop trading, and Iridius meets it at a disclosed premium instead of turning it away or mispricing it.
* **Distribution through the funnel.** Users on the chain self-custody Stock Tokens taken directly from a broker app, and they are reached through the wallet directory, the aggregators and the API. See [Ecosystem integrations](../chain/ecosystem.md).
* **Infrastructure has compounding returns.** Once a wallet or agent integrates the [API](../architecture/api.md), it continues to send flow with no extra acquisition cost.

## Costs

The costs are audits and the bounty, oracle and data services, RPC and indexing, the KYC provider's per-attestation fees, and the team. Fees accumulate in the `FeeCollector` and pay for these first, and after the module takes over, the [governance](../transparency/governance.md) log records how funds are allocated.
