---
description: "A hands-on guide to funding vaults: depositing, earning, monitoring and exiting."
---

# Liquidity provider guide

Providing liquidity on this venue means funding one or more [anchor vaults](../protocol/anchor-vaults.md) and earning the spread on the fills they make. It resembles underwriting a market-making book much more than it resembles farming: the income comes from genuine flow, and the risks are inventory risks, set out in full in [Liquidity provision](../protocol/liquidity-provision.md). Start there, and come back to this page for the step-by-step.

## Onboarding

Taking part requires an `LP` attestation, which at launch is issued to professional clients via the same flow used for trading eligibility. The vaults you fund hold Stock Tokens, so onboarding includes the [issuer-risk disclosure](../risk/issuer.md).

## Choosing markets

Every vault is a separate decision, and the [trade explorer](trade-explorer.md) publishes the information needed to underwrite each one:

* realised spread revenue and volume split by regime, from the listing date onward,
* the current inventory mix, and the distance between its skew and the band,
* the market's tier parameters and caps,
* a record of halts, each with its reason.

Tier A offers higher turnover and tighter spreads, while Tier C pays wider spreads on thinner flow that gaps more frequently. On purpose, there is no pooled product that makes this choice for you.

## Depositing

Deposit USDG, the market's token, or a combination. The vault prices the deposit at the guarded mid and mints shares in return. There is no lock-up and no entry fee. During the guarded launch every vault has a TVL cap, shown on its page, and once a vault reaches its cap it accepts no further deposits until the timelock lifts it.

## While deposited

Every fill raises value per share, and your position page displays this together with your portion of the current inventory. Things to keep an eye on:

* **Skew.** A vault held at the edge of the band quotes on one side only and earns less, and skew that never comes back signals that flow in that market keeps running in one direction.
* **Regime mix.** Fills in the closed regime carry the gap risk you are paid to bear, and the explorer shows what proportion of your vault's fills took place at the weekend.
* **Halts.** A halted vault accrues nothing, and you can still withdraw the whole time.

## Exiting

When you burn shares you receive a proportional, in-kind share of whatever USDG and tokens the vault holds at that point, in every vault state, without exception. Two things to plan for:

* Depending on the mix when you leave, you might exit holding more token and less USDG than you deposited, or the opposite. Converting the token leg back is a normal trade at normal cost.
* An exit during `CLOSED` leaves you with inventory that cannot be flattened cheaply until the underlying opens again. You can always leave; when you leave still deserves some thought.

## Taxes and accounting

Spread revenue and price movement both build up in share value, and most regimes treat an in-kind withdrawal as a disposal. The explorer exports complete per-position history as CSV, and how to interpret it is a matter for you and your adviser. Nothing in this documentation is tax advice.
