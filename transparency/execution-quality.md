---
description: On every fill, the venue scores its own execution publicly against the reference price.
---

# Execution quality

Since execution is what the venue sells, it is measured and reported the way infrastructure reports uptime: all the time, without filtering, and against a benchmark no one inside the venue can shift.

## The metric

For each fill: the signed gap in basis points between the realised price and the guarded Chainlink mid captured in the oracle round of the fill event. A positive value means the trader paid above mid, which is normal and equal to the spread; a negative value means price improvement, which RFQ competition can deliver by beating the vault. The mid used is the very one the fill was checked against, so anyone can recompute the metric exactly from chain data, and the venue has no way to substitute a kinder benchmark later.

## Published data

| Cut | Contents |
| --- | --- |
| Live | Rolling distributions by market, venue and regime, showing median, p90 and the worst fill |
| Daily | The complete fill-level dataset as a download, with no minimum size and nothing withheld |
| By regime | `OPEN` and `CLOSED` costs next to each other, so the weekend premium becomes a figure rather than a suspicion |
| RFQ | Aggregated maker price improvement relative to the vault quote at the same instant |
| Reverts | The rate of slippage-bound and band reverts, because failing fills is one way to make the successful ones look better |

## Interpreting the figures

* For a Tier A market, the median `OPEN` fill should land close to the base half-spread plus fee. Persistent drift above that means the parameters need retuning, and when that timelocked retune happens it will reference this dataset.
* The `CLOSED` distribution should be wider and more expensive, because that is gap risk being priced. It should never show fills near the edge of the band during calm conditions.
* When band margins suddenly shrink across many fills, that is the earliest signal of an oracle problem, and it is why the operations stack raises alerts on exactly the public figures shown on this page.

## Why bad days are published too

Leave things out and a dataset becomes marketing. Because everything is published, including the costly weekend fills and the reverts, the LP underwriting a vault, the maker calibrating quotes and the trader deciding whether Saturday's trade can wait all read the same record by which the venue is judged. Scoring itself honestly is how a venue gets better, and this dataset also serves, openly, as the venue's own tuning input.

The raw data is available at `/v1/execution-quality` in the [API](../architecture/api.md), and each aggregate on this page links to the fills behind it in the [trade explorer](../users/trade-explorer.md).
