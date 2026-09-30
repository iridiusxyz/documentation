---
description: The venue's entire activity, open to anyone who cares to look.
---

# Trade explorer

The trade explorer captures everything the venue does: each fill, each market's live state, each parameter and each change made to it, all built from chain events and linked back to them. You can open it without a wallet, since nothing in it belongs privately to anyone. This is where "transparently on-chain" either gets verified or turns out to mean nothing.

## Per market

| Panel | Contents |
| --- | --- |
| Live quote | Current bid, ask and mid with the complete breakdown, priced exactly as the router would price it right now |
| Regime | The active regime, the signals behind it (feed timestamp, stream market status), and how long it has been in force |
| Inventory | The vault's mix relative to target and band, along with skew direction and size |
| Parameters | Tier, spreads, clip, caps and band, all read directly from `ParamController` |
| History | Fills with itemised breakdowns, spread revenue by regime, and the halt log with reasons |
| Listing record | The tier evidence, the bytecode review result, the issuer disclosure, and proof-of-reserve status where a feed is available |

## Venue-wide

* Volume, number of fills and revenue, split by market, by venue (vault or RFQ) and by regime.
* A single figure for total vault inventory exposed to Robinhood Assets (Jersey) Ltd, as described in [Issuer risk](../risk/issuer.md).
* The [execution quality](../transparency/execution-quality.md) dataset: how far each fill landed from the oracle mid, aggregated and available to download.
* The governance log: each parameter proposal, past and pending, with its reasoning, timelock window and execution.

## Every number links to its source

Every figure links to the Blockscout event, transaction or contract read it came from. The explorer is built on the open-source indexer, so anyone can run it against any RPC and compare the results; if the explorer and the chain ever disagree, the chain is right and the difference is a bug to report.

## For auditors and integrators

All of the data shown here can be pulled raw from the [API](../architecture/api.md) at `/v1/markets`, `/v1/fills` and `/v1/execution-quality`, or downloaded as CSV, with full per-position LP histories for account holders. Researchers are free to use it. Our view is that an RWA market which no one can independently reconstruct does not really have a record at all.
