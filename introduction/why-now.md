---
description: The 2026 market shifts that made an RWA-native venue worth building.
---

# Why now

Three conditions had to hold simultaneously before a venue like this could work. Tokenized equities needed to act like ordinary on-chain assets, backed by pricing that people could trust. Real demand to trade them had to exist. And the venues already meeting that demand had to be leaving a significant gap. In 2026 every one of those conditions is met.

## Tokenized equities are now an asset class of their own

Excluding stablecoins, transferable RWAs on public chains grew from about $7.9B at the end of 2024 to about $21B at the start of 2026, and stand at roughly $38.7B today. Of all the categories in that total, tokenized equities are compounding the fastest.

| Category (rwa.xyz, 28 Aug 2026) | On-chain, transferable value | Notes                                                                                   |
| ------------------------------- | ---------------------------- | --------------------------------------------------------------------------------------- |
| Stablecoins                     | ~$303B                       | The final settlement layer                                                              |
| Tokenized US Treasuries         | ~$16.0B                      | USYC $2.9B, BUIDL $2.8B, USDY $2.2B, BENJI ~$2.4B                                       |
| Tokenized credit                | ~$7.5B distributed           | Nearer $35B once non-transferable "represented" assets such as Figure HELOCs are included |
| Commodities                     | ~$3.1B                       | XAUT, PAXG                                                                              |
| **Tokenized stocks**            | **~$2.6B**                   | Ondo Stocks over $1B TVL; xStocks past $25B cumulative volume; Robinhood, Coinbase, Binance |
| Private equity and VC           | ~$1.6B                       |                                                                                         |
| Real estate                     | ~$175M                       |                                                                                         |

On their own, tokenized equities rose from about $424M in mid-2025 to about $2.59B by August 2026. Providers' figures differ by around 20 percent, largely depending on whether they include non-transferable assets.

## Issuance is solved; trading is not

Almost all tokenized-equity volume today runs through centralised order books, so it is custodial, off-chain and invisible to outsiders. The on-chain alternative is AMMs built for crypto-native pairs, and the ways they fail with RWAs are entirely predictable:

* **Pricing off reserve ratios drifts.** A constant-product pool holds no view on value. With thin books, tokenized equities have traded well above the underlying, and the dislocations of the 2025 launch weeks are on record. See [Lessons from RWA trading](../risk/lessons.md).
* **LPs suffer adverse selection.** Each tick on the underlying exchange leaves the pool's curve behind, and arbitrage pockets the gap before the curve catches up. Passive curve liquidity on an asset priced elsewhere amounts to a permanent subsidy for the fastest trader.
* **Nights and weekends go unpriced.** The regular session adds up to about 32 hours a week. Price Saturday the same as Tuesday and gap risk is mispriced across roughly 80 percent of the calendar.
* **Pools break on corporate actions.** A split or reverse split changes the token's price overnight, and a pool that is unaware of it just gives the difference away.

Minting with the issuer and redeeming back is an exit route, not a market. No on-chain venue of meaningful size has yet treated these assets as what they actually are: priced externally, tied to a session calendar, and exposed to corporate events.

## Robinhood Chain removes the last barrier

Robinhood Chain mainnet went live on 1 July 2026. It is the only L2 where a regulated broker issues 1:1-backed tokenized equities as plain ERC-20s, alongside 24/5 Chainlink feeds, Data Streams with a market-status field, sub-cent transaction costs, ERC-4337 account abstraction and permissionless deployment. Assets, pricing and users are all in place; what is missing is a venue that understands the assets. See [Why Robinhood Chain](../chain/why-robinhood-chain.md).

## Today's venues point the wrong way

* Kraken's xStocks, Bybit and Binance push their volume through centralised books, so it stays custodial and off-chain, precisely the situation tokenization was supposed to retire.
* Ondo Global Markets routes orders into US exchange liquidity via its own broker relationship, which gives deep pricing but ties it to a single issuer and to US market hours.
* Uniswap v4 introduced permissioned pools in July 2026, addressing who is allowed to trade but leaving untouched how an RWA should be priced.

A non-custodial, oracle-anchored, session-aware venue takes nothing away from those strengths. Iridius works next to them and covers the ground they leave uncovered. See [Design principles](design-principles.md).
