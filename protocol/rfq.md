---
description: How competing makers price block-size trades and how those trades settle atomically on-chain.
---

# RFQ settlement

When a trade is larger than the vault clip, or whenever a maker can simply beat the vault's price, execution moves to request-for-quote: makers sign short-lived prices off-chain, and the taker settles the best one in a single atomic on-chain transaction. Because signing is free for makers, quotes can flow continuously, and the chain is only paid when a fill actually happens.

## The quote

Makers send an EIP-712 typed message:

| Field | Meaning |
| --- | --- |
| `maker` | Signing address, which must hold a `MAKER` attestation |
| `tokenIn`, `tokenOut` | The pair, with USDG always on one side |
| `amountIn`, `amountOut` | Exact size and exact price, with no partial fills |
| `taker` | The requester, or zero for an open quote |
| `expiry` | Unix seconds; quotes live for seconds, not minutes |
| `nonce` | Usable once, and can be cancelled in bulk |

The quote service sends each request to all connected makers, collects signatures over a few hundred milliseconds, and returns the best one next to the vault price. The router settles whichever of the two is better for the trader.

## Settlement

`RfqSettlement.settle(quote, signature)` checks the maker's signature and attestation, the taker's `TRADER` attestation, the nonce, the expiry, and that the implied price falls within the [oracle band](pricing-and-spreads.md). The assets then move directly: `tokenIn` goes from taker to maker, `tokenOut` from maker to taker, and the RFQ fee to the `FeeCollector`. The maker's side is drawn from the maker's own wallet via a standing Permit2 allowance, so Iridius holds nothing at any point before, during or after the fill.

If the quote has expired, its nonce has been used or cancelled, or its price has moved outside the band by the time of inclusion, the transaction reverts. Apart from any hedge the maker decided to place, this costs the maker nothing.

## What draws makers

* **Flow that can be hedged.** Each request comes with size and direction known upfront, in assets with a liquid underlying. After filling 200,000 USDG of tokenized NVDA, a maker can hedge within seconds while the market is open, or price the weekend gap on purpose while it is closed.
* **A subsidy for skew.** The side on which a vault will pay to be lifted is [public state](pricing-and-spreads.md). Makers who track skew earn a spread for restoring inventory to where it should be, which is precisely how rebalancing is meant to work.
* **Neither exchange fees nor a queue.** Winning requires a signature. Losing costs nothing.

## Failure containment

All of the vault path's guards still apply: the band limits the damage from a bad quote, attestations control who is allowed to quote, expiries cap how long a stale price can live, and atomic settlement unwinds the entire fill if any leg fails. A compromised or hostile maker can do no more than fill trades within the band, which is also the most an aggressive but honest maker could do.

For onboarding, tooling and the quote protocol specification, see [For market makers](../users/market-makers.md) and [API and SDK](../architecture/api.md).
