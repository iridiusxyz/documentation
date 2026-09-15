---
description: The asset that sits on the other side of every Iridius market.
---

# Quote asset: USDG

All markets are priced in **USDG**, the Paxos Global Dollar. It is the unit for spreads, fees and vault accounting, and every token-to-token trade routes through it.

## Why USDG

| Reason | Detail |
| --- | --- |
| Native to the chain | USDG is issued natively on Robinhood Chain instead of arriving via a bridge, so bridge risk never enters the settlement path. |
| The chain's dollar | USDG already underpins Robinhood Earn and the Morpho lending markets on the chain. The dollar a trader arrives with is, as a result, the dollar the venue quotes in. |
| Regulatory footing | In the EU, USDG is covered by MiCA, which is relevant for a venue whose first market is shaped by the Stock Token jurisdiction list. |
| Clean semantics | A standard ERC-20 without fee-on-transfer or rebasing, which is precisely the foundation vault accounting requires. |

## A single quote asset, on purpose

Sending everything through a single quote asset concentrates liquidity rather than splitting each Stock Token across a handful of stablecoin pairs. Prices become directly comparable, and two-leg [routing](../protocol/routing.md) stays simple enough for the chain to verify. The trade-off is reliance on a single issuer, which is disclosed with the venue's other dependencies in the [Risk framework](../risk/framework.md).

## Further quote assets

Market configuration is keyed on the quote asset, so adding a second one (USDC via CCTP once bridged liquidity is confirmed, or USDe, already live on the chain) would produce a new, isolated group of markets and leave existing markets untouched. A new quote asset must first have confirmed bridged liquidity and pass the bytecode review applied to every listed asset, and it then goes through the timelock like any other parameter.

## Precision and decimals

USDG uses six decimals and Stock Tokens use eighteen. `OracleRouter` returns prices in quote-asset base units per whole token, vault value is computed as `usdgBalance + tokenBalance × price / 10^18`, and the invariant suite exercises that arithmetic across both decimal regimes, including the rounding direction on share mints and burns.
