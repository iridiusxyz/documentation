---
description: The swap layer for tokenized real-world assets on Robinhood Chain, with every price, fill and fee recorded on-chain where anyone can verify it.
---

# Iridius at a glance

On Robinhood Chain, Iridius is where tokenized real-world assets trade, and at no point does the design ask you to give up custody of them. Stock Tokens (tokenized AAPL, NVDA, SPY and the rest of the catalogue) trade against USDG, with tokenized treasuries and gold coming later, and prices come from the same Chainlink feeds the broader market relies on. The quoted price, the executed trade and the fee charged are all recorded on Robinhood Chain, so anyone watching can read them back.

## The idea in brief

A generic AMM prices an asset by looking at its own reserves. For something whose price originates on-chain that works; for something priced on an exchange elsewhere it does not. Iridius turns this around. Each market is backed by an [anchor vault](protocol/anchor-vaults.md) that starts from the Chainlink mid and layers on a spread set by three inputs: the session the underlying is trading in, the current shape of the vault's inventory, and the asset's tier. When the underlying market closes, the spread widens, the maximum size drops, and the ticket says so on both counts. Trades too large for the vault's clip move to [RFQ](protocol/rfq.md), where professional makers sign quotes off-chain and settlement happens on-chain as a single step. The [router](protocol/routing.md) prices both routes, picks the cheaper, and shows the trader one number with the mid, spread and fee itemised separately.

## How it differs from an AMM

| Property           | Generic AMM pool                                | Iridius                                                              |
| ------------------ | ----------------------------------------------- | -------------------------------------------------------------------- |
| Price discovery    | Set by the reserve ratio, whatever it says      | Starts at the Chainlink mid, with guards setting the outer bounds    |
| Closed markets     | Saturday's quote looks just like Tuesday's      | Spreads widen, clips shrink, and the ticket states both              |
| Corporate actions  | Breaks, or needs rebuilding somewhere else      | The ERC-8056 multiplier feeds the event into the price, and the market pauses while it happens |
| LP economics       | Leaks value to arbitrage whenever price moves   | Earns spread on quotes placed around the oracle                      |
| Large trades       | Pays to walk the curve                          | Attested makers compete for the order through RFQ                    |
| Compliance         | None                                            | Applied at the protocol's entry point                                |
| Fees               | Hidden inside the curve                         | Itemised on the quote and written to the chain                       |

For pairs that exist wholly on-chain, constant-product maths does the job. Aim it at an asset with an outside reference price, a trading calendar and a record of corporate actions, though, and it leaks value to arbitrageurs while the quote drifts over quiet weekends. Those assets call for market structure built specifically for them, and Iridius exists to supply it.

## Key facts

| | |
| --- | --- |
| Chain | Robinhood Chain (Arbitrum Nitro L2) |
| Quote asset | USDG (Paxos Global Dollar) |
| Launch assets | Robinhood Stock Tokens |
| Next assets | The remaining long tail of Stock Tokens, followed by tokenized treasuries and gold (sequence in the [Asset roadmap](assets/asset-roadmap.md)) |
| Execution | Oracle-anchored vaults for everyday size, RFQ for blocks, and one router across the two |
| Pricing | Chainlink Data Feeds and Data Streams, aware of market sessions and protected by guards |
| Custody | No one takes custody of your assets. They move only at settlement, and vault inventory sits in immutable contracts |
| Fees | One protocol fee, shown line by line, with nothing skimmed quietly from the spread |

## Out of scope for Iridius

* Take custody of customer assets, or act as an on-ramp or off-ramp between money and tokens.
* Issue, mint or redeem RWAs. Regulated issuers own the underlying layer, and Iridius operates the secondary market on top.
* Offer lending, leverage or perpetuals.

## Where to go from here

* First time here: begin with [Why now](introduction/why-now.md) and follow it with [Design principles](introduction/design-principles.md).
* Want the mechanics: read the [Protocol overview](protocol/overview.md), followed by [Anchor vaults](protocol/anchor-vaults.md), [Pricing and spreads](protocol/pricing-and-spreads.md) and [Trading regimes](protocol/trading-regimes.md).
* Integrating with Iridius: head to [System overview](architecture/overview.md), [Smart contracts](architecture/smart-contracts.md) and [API and SDK](architecture/api.md).
* Assessing the risks: see the [Risk framework](risk/framework.md) and [Lessons from RWA trading](risk/lessons.md).
* Trading or providing liquidity: go to [For traders](users/traders.md) and [For liquidity providers](users/liquidity-providers.md).
