---
description: How a market gets through splits, dividends and similar events unharmed.
---

# Corporate actions and dividends

Sloppy RWA venues lose money here without ever noticing. After a 10-for-1 split a token's price resets overnight, and any pool nobody informed hands the gap to the first arbitrageur who spots it. Iridius gets most of its protection for free from the design of the asset and the oracle, and a halt handles what is left.

## Dividends: handled by design, with nothing to do

Robinhood Stock Tokens implement ERC-8056, where `uiMultiplier()` reports how many underlying shares each token represents, and a dividend increases that figure instead of being paid out separately. The Chainlink price already includes the multiplier. For a trading venue, every result of this is helpful:

* There is never a dividend token, so nothing needs claiming, sweeping or splitting between LPs and traders.
* The vault has no ex-dividend cliff to misprice, as value builds up gradually in the token and its feed.
* Vault accounting contains no dividend logic at all. The multiplier's effect on an LP's share value looks exactly like any other price movement.

The trade explorer reads `uiMultiplier()` together with the price, which lets it display holdings both in shares and in tokens.

## Splits, mergers and delistings: halt, absorb, resume

A split, merger, symbol change or similar event causes the Chainlink feed to set `oraclePaused()`. Once that flag is up the market moves to [`HALTED`](trading-regimes.md) automatically: quoting stops, RFQ no longer settles in that market, and deposits and withdrawals continue as normal.

When the feed resumes, its price and multiplier already reflect the event, so the market restarts quoting from the adjusted value. The vault's inventory needs no adjustment because it held the same tokens the whole time; a split changes the figure the feed reports, not the contents of the contract.

| Event | During | After |
| --- | --- | --- |
| Dividend | Nothing to do; multiplier and price grow together | Nothing to do |
| Split or reverse split | `HALTED` for as long as `oraclePaused()` is set | Quoting restarts on the adjusted feed with inventory unchanged |
| Merger, acquisition | `HALTED` | Resumes, or moves to [retirement](#retiring-a-market-after-delisting) if the token is being wound down |
| Delisting of the underlying | `HALTED` | Retirement |

## Retiring a market after delisting

If an underlying disappears or an issuer shuts a token down, the timelock sets that market to `RETIRED`: quoting stops for good and withdrawal is all that remains. LPs take out their proportional inventory in kind and can then use the issuer's own redemption process if they choose; the protocol never holds a position through a wind-down. As with any parameter change, the rationale for retiring the market is recorded in the governance log.

## Risk that remains

The design relies on the feed pausing before anyone can trade against a stale price. Chainlink's equity feeds do raise `oraclePaused()` around corporate actions, and if a feed goes quiet instead of pausing, the [staleness guard](../risk/oracles.md) catches it, because silence alone halts the market. What is left is a feed confidently publishing wrong prices through an event, which is the same oracle risk every fill already carries, and the [band](pricing-and-spreads.md) caps the loss on any single fill at the band's width.
