---
description: Per-asset contracts that hold inventory and keep a two-sided quote live in each market.
---

# Anchor vaults

Every market is a vault. It holds a single Stock Token and USDG, backed by LPs who fund both sides. As long as it is not halted, the vault keeps a bid and an ask posted around the oracle mid, fills swaps up to its clip size, and passes the spread it collects back to its funders.

## Inventory model

Each vault steers towards a configured mix (initially an even split by value) and never strays outside a hard band drawn around that target.

| Parameter | Meaning | Tier A initial value |
| --- | --- | --- |
| `targetRatioBps` | The share of vault value meant to sit in the Stock Token | 5,000 (50%) |
| `inventoryBandBps` | How far from target the mix can drift before one side of the quote turns off | 2,000 (±20%) |
| `maxClip` | The largest single swap the vault accepts | 50,000 USDG |
| `dailyVolumeCap` | Ceiling on volume per market while the guarded launch lasts | 2,000,000 USDG |

When buyers keep arriving, token inventory runs down and USDG accumulates, so the vault ends up short of the asset. At that point [skew pricing](pricing-and-spreads.md) improves the bid and worsens the ask, in effect paying makers and arbitrageurs to restore the balance. If the one-way flow persists all the way to the band edge, the vault stops quoting that side instead of taking on unlimited inventory risk. The opposite side keeps quoting the whole time.

## Holding inventory instead of a curve

A constant-product pool also holds inventory, yet it derives its price from that inventory, so each move in the reference price leaves it quoting a stale figure that arbitrageurs then harvest. An anchor vault takes its price from the oracle and lets inventory shape only the spread around it. As a result the vault is never the last party to learn what an asset is worth, and its LPs keep the spread rather than subsidising someone else's arbitrage. The trade-off is acknowledged plainly: the vault depends on the oracle, and [Oracles and market sessions](../risk/oracles.md) lists every guard that protects that dependency.

## Value accounting

Vault value is `usdgBalance + tokenBalance × mid`, with the mid taken from the guarded oracle price. On top of that total sits ERC-4626-style accounting: a deposit mints shares at the current value per share, spread income raises that value, and a redemption burns shares in exchange for a proportional in-kind portion of both assets. Since redemptions are paid in kind, an LP leaving never forces the vault into a trade, and a pause cannot trap LP capital. The mechanics and economics are covered in [Liquidity provision](liquidity-provision.md).

## Lifecycle

| State | Meaning |
| --- | --- |
| `ACTIVE` | Both sides quoting, subject to what the regime permits |
| `ONE_SIDED` | At the band edge, so only the corrective side quotes |
| `HALTED` | No quoting, because of a stale oracle, an `oraclePaused()` flag, a pending corporate action or a guardian pause. Deposits and withdrawals carry on |
| `RETIRED` | Delisted: quoting has stopped for good and withdrawals are all that is left |

`VaultFactory` deploys each vault with the parameters set at [listing](../assets/listing-framework.md), and each of those parameters is a `ParamController` value that can change only through the timelock.

## Invariants

The contract maintains the following properties, and the test suite proves each of them:

* Whatever the state or parameter set, no fill settles outside the oracle band.
* A swap can never reduce value per share, because spread revenue is non-negative by design.
* Withdrawals remain available in both `HALTED` and `RETIRED`.
* The vault cannot quote its own inventory beyond the band.
