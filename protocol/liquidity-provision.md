---
description: What LPs deposit into the vaults, what they get back, and precisely which risks lie between the two.
---

# Liquidity provision

LP capital is what the vaults trade with, and the spread from each fill goes back to the people who provided it. The design aims to pay LPs for a genuine service, supplying immediacy in a market that has an external reference price, and not for acting as the reluctant counterparty to someone's arbitrage.

## Depositing

Deposits are made per market, open to any holder of an `LP` attestation. You can deposit USDG, that market's Stock Token, or a combination; the deposit is valued at the guarded oracle mid and turned into shares at the current value per share. There is no entry fee and no lock-up.

Shares are accounted for under ERC-4626. Value per share is `(usdgBalance + tokenBalance × mid) / totalShares` and rises as spread revenue comes in. Shares can be transferred only to other attested LPs.

## Withdrawing

When shares are burned, the LP receives a **proportional, in-kind** slice of the vault's current holdings: burn 1% of the shares and you get 1% of the USDG plus 1% of the tokens. In-kind withdrawal was a deliberate choice, with three consequences:

* An exit never requires the vault to trade, so it cannot move the market or be sandwiched.
* Withdrawal works in every vault state, including `HALTED` and `RETIRED`. No pause, halt or parameter can keep LP capital locked in.
* Any skew the vault holds is reflected in the mix the LP gets back. An LP who wants only USDG swaps the token leg like any other trader.

## How LPs earn

On every fill, the realised spread minus the protocol's 10% [share](fees.md) feeds continuously into value per share. There are no emissions and no points; the yield is just what the market pays for immediacy. The [trade explorer](../users/trade-explorer.md) shows realised spread revenue per market in real time, so vaults can be underwritten on a track record rather than a projection.

## The risks LPs carry

Stated bluntly, since nobody should deposit without having priced these in:

| Risk | Nature | Mitigation |
| --- | --- | --- |
| Price exposure | Holding the Stock Token means following the stock. By design an LP is long about half the time | Each market stands alone; the LP chooses the assets |
| Gap risk | Fills overnight and at weekends may be at prices that gap by the open | Closed-regime multipliers and reduced clips are there specifically to price this |
| Oracle risk | A wrong mid produces wrong quotes | The guards described in [Oracles and market sessions](../risk/oracles.md); the band limits the cost of any one fill |
| Issuer risk | Each Stock Token is a claim on its issuer | Explained in [Issuer risk](../risk/issuer.md); disclosed, not diversified away |
| Adverse flow | Flow that only ever runs one way holds the vault at its band edge | The vault quotes one side only instead of absorbing unlimited inventory |

LPs are **not** exposed to leverage, liquidation, losses spilling over from other markets, or any asset they did not select. Vaults never interact with one another.

## Guarded launch

During the guarded phase, per-market TVL caps work together with the daily volume caps to limit total exposure while parameters are calibrated on live data. Lifting them is a timelocked decision made as the [execution quality](../transparency/execution-quality.md) record builds up, following the schedule in the [Roadmap](../roadmap/phases.md).
