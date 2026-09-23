---
description: The reference front-end at iridius.xyz/platform and the standards it must meet.
---

# Platform

The reference client is a Next.js application at [iridius.xyz/platform](https://iridius.xyz/platform), built on the same public [API](api.md) and the same contracts available to every integrator. It never handles keys, funds never flow through it, and it has no privileged route behind it. Its only job is to make honest mechanics easy to read.

## The ticket

The swap ticket is the heart of the product, and it displays the [pricing formula](../protocol/pricing-and-spreads.md) instead of hiding it:

* Mid, spread and fee shown as three labelled figures in bps and USDG, with a link to the oracle round.
* A regime badge right on the ticket: "US markets closed. Weekend spread ×3 applies." Nobody finds out about the session only after filling.
* An all-in rate protected by the slippage bound, plus a countdown to the end of RFQ quote validity.
* Both legs displayed and itemised on two-leg swaps.

Each fill returns a receipt with the on-chain breakdown and a link to Blockscout. The event carries the identical decomposition, so it is provable every time that the amount quoted is the amount charged.

## Onboarding

Users can connect Robinhood Wallet, MetaMask or any standard wallet, or create an embedded smart account with a passkey via Alchemy Account Kit. Eligibility onboarding takes the user through the attestation flow with the KYC provider, and eligibility state is displayed before any signature, so an ineligible user sees the rule that applies rather than a revert. On a first swap, the Permit2 approval and the swap are combined into a single sponsored user operation.

## Portfolio

A wallet-centred view of listed holdings: balances expressed in both token and share terms through `uiMultiplier()`, valuations taken from the same guarded oracles the vaults price against, cost basis and realised PnL reconstructed from on-chain history, and LP positions with value per share, accrued spread income and the present inventory mix of each vault.

## Explorer surfaces

The [trade explorer](../users/trade-explorer.md) and [execution quality](../transparency/execution-quality.md) pages sit inside the platform and open with no wallet connected. Transparency surfaces are public by nature, so nothing in the venue's record is gated behind a login.

## Experience standards

The bar comes from [Design principles](../introduction/design-principles.md): calm, technical and precise. In practice:

* Numbers aligned in tabular numerals, bps and USDG never sharing a column, and timestamps in the reader's time zone with UTC shown on hover.
* Empty, loading and error states phrased as full sentences explaining what happened and what to do next.
* Honest degradation: if the quote service is down, the platform quotes vault-only from the RPC and tells you so; if the RPC is down, it says that instead of spinning forever.
* No dark patterns at all: no slippage preset above the default, no manufactured urgency, no hidden fees. The venue's economics stand up well once they are understood.

## Self-hosting

The platform's source is public. Run `pnpm dev` against any RPC and the public API to get the full experience, apart from the hosted KYC flow, which can be replaced by any issuer the registry accepts.
