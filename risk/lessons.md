---
description: Lessons from the first years of tokenized-asset trading, and where the venue builds each one in.
---

# Lessons learned in RWA trading

On-chain rails change how trades settle; they leave market structure as it was. The years 2020 to 2026 show exactly what happens when tokenized assets pass through venues that ignore what those assets actually are. Iridius treats that history as a design brief, not as something to argue with.

## Case studies

### FTX and CM-Equity tokenized stocks (2020 to 2022)

| | |
| --- | --- |
| What happened | Through a custodial arrangement with CM-Equity, FTX sold tokenized stocks. After FTX collapsed in November 2022, holders found they owned bankruptcy claims, and the arrangements behind the tokens had never been transparent. |
| Lesson | Custody is where the risk lives. A tokenized share held at a custodial exchange is worth only as much as that exchange. |
| In Iridius | The venue never takes custody of anything. Assets sit in user wallets or in immutable vault contracts, and LPs can leave in kind from any state. If the venue holds nothing, the venue can lose nothing. |

### July 2025: dislocations at the tokenized-equity launches

| | |
| --- | --- |
| What happened | During the first weeks of tokenized stocks on Solana, thin DEX pools allowed tokens to trade well above the underlying, sometimes at several multiples of fair value. Those pool prices came from reserves, with no link to any reference. |
| Lesson | A reserve ratio has no view on fair value. On a thin book it quotes whatever figure the arithmetic produces. |
| In Iridius | Every quote starts from the Chainlink mid and works outward, and the band makes "no fill lands further than this from the reference price" an invariant enforced in the contracts, no matter how thin the market becomes. |

### The recurring cost of curve liquidity on reference-priced assets

| | |
| --- | --- |
| What happened | Not a single event but a steady leak: each move in the reference price gives arbitrageurs a profit paid by LPs in constant-product RWA pools, a cost the literature names loss-versus-rebalancing. When an outside exchange keeps repricing an asset, a passive curve routinely moves LP value out of the pool. |
| Lesson | When an asset's price is set somewhere else, the venue should consume that reference price, not attempt to discover it again. |
| In Iridius | Anchor vaults quote around the oracle, so LPs earn spread from real flow and not from the scraps arbitrage leaves. Rebalancing is paid for in the open through skew rather than draining away. |

### Edel Finance and wGOOGLx (July 2026)

| | |
| --- | --- |
| Loss | Roughly $403K of bad debt |
| What happened | The Chainlink oracle for tokenized Google stock stayed accurate the whole time. The trusted input was a wrapper token's exchange rate, pushed to 78 times its true level, and the valuation built on top of it. |
| Lesson | Price the token you actually hold. Every derived value is an attack surface. |
| In Iridius | `OracleRouter` reads the feed for the token held in the vault and nothing else. Wrappers, vault shares and derived rates are never listed and never priced. |

### Weekend gaps on always-open tokens

| | |
| --- | --- |
| What happened | Tokens that trade around the clock over underlyings open 32 hours a week drift through the weekend and then gap at the open. Venues that quoted weekends at weekday spreads left their LPs losing every Monday morning, and venues that shut down simply gave those hours back to custodial books. |
| Lesson | The session belongs to the asset, and pricing it belongs to the venue. |
| In Iridius | When the underlying is closed, the regime engine widens the spread, shrinks the clip, shows both on the ticket, and draws its halts from the oracle's own market status instead of a hard-coded calendar. |

## What the cases share

Each case comes down to one of three causes: the venue holding custody, prices cut loose from the reference, or values derived when they should have been fed. The design removes all three:

1. **Nothing held in custody**, at any point: settlement is atomic and withdrawal happens in kind.
2. **Every price has an anchor**: every fill is bounded by the guarded Chainlink mid.
3. **Nothing derived**: a market exists only for the exact token with its own feed.

Issuer, gap, sequencer and contract risk all remain, and all remain real; the rest of this section covers each of them together with what limits it.
