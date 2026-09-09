---
description: One contract that combines vaults and RFQ into a single price.
---

# Routing

Traders interact with exactly one contract, `SwapRouter`. It has four responsibilities: checking eligibility, choosing the venue, building multi-leg trades, and enforcing the limits that apply to every fill.

## Venue selection

For a given size, the router compares the anchor vault (provided the size is within the clip and the regime allows quoting) with any valid maker quotes passed in the call, and settles whichever gives the trader more output. The comparison happens on-chain and is unambiguous: the vault quote is recomputed from oracle and vault state in the same transaction instead of being trusted from a front-end, and the maker quote is checked by its signature. A front-end therefore cannot misroute a trade to a worse price, since the router refuses to settle a supplied path that the vault beats.

## Two-leg swaps

A token-to-token swap runs as two legs via USDG, executed together:

1. The first leg sells `tokenIn` to its vault or to a maker for USDG.
2. The second leg uses that USDG to buy `tokenOut`, again from a vault or a maker.

Venue choice is made per leg, so a vault can fill one leg and a maker the other. Every leg is subject to its own market's band and regime, and if either fails, both are reversed. The fill event records both legs with itemised pricing, and the trader's signed slippage bound covers the overall end-to-end rate.

## Conditions on every fill

| Check | Failure mode prevented |
| --- | --- |
| Caller holds a `TRADER` attestation | Trading by ineligible parties |
| Oracle band, checked per leg | A fill straying from the guarded reference price |
| Regime allows quoting | Trading during a halt |
| Clip and daily volume caps | Flow beyond what the guarded launch permits |
| Trader's slippage bound and deadline | Filling against state that has moved on |
| Permit2 pull equal to the signed amounts | Allowances being spent on anything else |

## Ordering and MEV

Robinhood Chain's sequencer orders transactions first-come-first-served, with preconfirmations of about 100 ms, which rules out the public-mempool sandwich as it currently exists on Ethereum. The design does not depend on that continuing. Every fill is already bounded by the signed slippage limit and the oracle band, so any ordering edge can capture at most the width the trader accepted beforehand. If the chain enables Timeboost or otherwise changes ordering, the review that follows is recorded in the governance log. See [Sequencer and chain risk](../risk/sequencer-and-chain.md).

## Integration surface

The router is open to any caller. As a public contract with a stable interface, it ties eligibility to the caller and not to the front-end they came through. Wallets, portfolio tools and agents get prices from the [quote API and SDK](../architecture/api.md) and submit directly to the contract. The reference front-end at [iridius.xyz/platform](https://iridius.xyz/platform) has no privileged access of any kind.
