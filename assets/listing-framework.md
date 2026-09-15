---
description: What an asset must demonstrate before its market opens, and the tier system that then sets its parameters.
---

# Listing framework

A listing is decided by a checklist, not by anyone's view. An asset meets the bar for a tier or it fails to; the evidence is published with the listing, and the tier determines the parameters with no further judgement involved.

## The bar for every listing

These apply regardless of tier. Each market must satisfy all of them:

| Requirement | Why |
| --- | --- |
| A live Chainlink Data Feed on Robinhood Chain for that exact token | Anchor pricing must value what the vault actually holds, never a wrapper and never a rate derived from one |
| Data Streams coverage with market status, or an equivalent session signal | Without an authoritative session source the [regime engine](../protocol/trading-regimes.md) cannot operate |
| A regulated issuer with published terms and one-for-one backing | The venue lists claims on real assets, not synthetics |
| A bytecode review of the deployed token | Pause, freeze, blacklist and forced-transfer roles are discovered before listing instead of after, and the result is published |
| ERC-20 with standard semantics | No fee-on-transfer, and no rebasing other than ERC-8056-style multipliers already reflected in the feed |

An asset that fails even one requirement receives no tier. Nothing can override this.

## Tiers

Every market parameter is set by tier. Starting values:

| Parameter | Tier A | Tier B | Tier C |
| --- | --- | --- | --- |
| Base half-spread | 10 bps | 20 bps | 40 bps |
| Max skew term | 15 bps | 25 bps | 50 bps |
| Oracle band | 75 bps | 150 bps | 300 bps |
| Regular clip | 50,000 USDG | 20,000 USDG | 5,000 USDG |
| Daily volume cap (guarded launch) | 2M USDG | 750K USDG | 200K USDG |
| Vault TVL cap (guarded launch) | 5M USDG | 2M USDG | 500K USDG |

Assignment rests on three inputs: the liquidity of the underlying (its average daily volume and typical spread on its primary market), the feed's update frequency, and the token's distribution across on-chain addresses. Every market publishes its assignment along with the supporting evidence, and changing a market's tier is a timelocked parameter change like any other.

* **Tier A**: index ETFs and mega-caps such as SPY, QQQ, AAPL, MSFT and NVDA, which have deep underlying books and frequently updated feeds.
* **Tier B**: liquid single names outside the mega-cap group.
* **Tier C**: the whole long tail. Rather than excluding names for wider underlying spreads and slower feeds, the venue prices them in through a wider band and spread, since serving the tail well is part of its purpose. That exposure stays honest because of caps.

## Delisting

When an asset no longer meets the requirements, its market is retired: the feed disappears, the issuer winds down the token, or a bytecode change adds a disqualifying control. Retirement passes through the timelock, is published with its reasoning, and leaves the vault open only for withdrawals. See [Corporate actions and dividends](../protocol/corporate-actions.md).

## Adding a new asset class

Tokenized treasuries and gold follow the same checklist, each with one extra requirement. NAV-type assets need a NAV-consistent feed and trade within a tight band around NAV, and any bridged asset needs the bridge review set out in the [Asset roadmap](asset-roadmap.md).
