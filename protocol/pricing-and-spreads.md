---
description: How every vault quote is calculated, and where each input to that calculation is read from.
---

# Pricing and spreads

All vault quotes are built the same way. No price involves any discretion: every term is a public contract read, so anyone who wants to can reconstruct any quote from chain state.

## The formula

```
ask = mid × (1 + (baseHalfSpread × regimeMultiplier + skewTerm + feeBps) / 10_000)
bid = mid × (1 − (baseHalfSpread × regimeMultiplier − skewTerm + feeBps) / 10_000)
```

| Term | Source | What it prices |
| --- | --- | --- |
| `mid` | `OracleRouter`, which supplies the guarded Chainlink price | The asset's current value |
| `baseHalfSpread` | Tier parameter | Holding inventory during the regular session, plus any residual oracle latency |
| `regimeMultiplier` | [Regime](trading-regimes.md) state | Gap risk from extended hours or a closed underlying market |
| `skewTerm` | Vault inventory | The cost of moving the vault further from target, signed so that the corrective side is negative |
| `feeBps` | `ParamController` | The protocol's fee, shown as a separate line on the ticket |

## Initial parameters

Values are in basis points, set by [tier](../assets/listing-framework.md), and can change only through the timelock.

| Tier | Base half-spread | Regular clip | Examples |
| --- | --- | --- | --- |
| A | 10 bps | 50,000 USDG | SPY, QQQ, AAPL, MSFT, NVDA |
| B | 20 bps | 20,000 USDG | Liquid single names not in Tier A |
| C | 40 bps | 5,000 USDG | The long tail of Stock Tokens |

| Regime | Multiplier | Clip factor |
| --- | --- | --- |
| `OPEN` | 1.0 | 1.0 |
| `EXTENDED` | 1.5 | 0.75 |
| `CLOSED` | 3.0 | 0.5 |
| `HALTED` | No quotes | 0 |

A Tier A trade in regular hours costs about 10 bps over mid plus the fee. The same trade on a Saturday comes closer to 30 bps, and the ticket explains the difference. These are starting values; the realised figures are published on the [execution quality](../transparency/execution-quality.md) page, and any retuning based on them goes through the timelock.

## The skew term

`skewTerm = maxSkewBps × (currentRatio − targetRatio) / inventoryBand`

The term grows linearly with drift and caps at `maxSkewBps` (initially 15 bps for Tier A) when the band edge is hit. Its sign carries the logic: when the vault holds too many tokens, the ask eases and the bid stiffens, so traders who help the vault pay less and those who make things worse pay more. At the edge, the harmful side stops quoting altogether. Nothing more is involved in rebalancing. There are no auctions, no keeper trades and no manual intervention; the skew just pays outside participants to do the job.

## The band

Separately from the spread calculation, `SwapRouter` enforces a hard band around the mid (75 bps for Tier A at launch, wider for B and C), and no fill from either the vault or RFQ can clear beyond it. It is the final safeguard against a bad quote, a forged maker signature or a misconfigured parameter: no matter what failed upstream, a fill further from the guarded oracle price than the band permits will revert.

## What the ticket shows

The ticket breaks the formula down term by term: the mid along with its source oracle round, the spread in bps and USDG, the protocol fee in bps and USDG, the regime badge, and the all-in price to which the slippage bound applies. The chain enforces exactly what was signed, and if state changes so that the fill would be worse than the signed bound, the transaction reverts instead of going through.
