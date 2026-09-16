---
description: The asset classes that come after Stock Tokens, and the preconditions each one needs.
---

# Asset roadmap

The venue expands one asset class at a time, in the order that each class's listing requirements can actually be satisfied on Robinhood Chain. A stage opens when the infrastructure is in place, not when someone announces that it will be.

## Stage 1: Stock Tokens, live with the tail expanding

Tier A launched first. Tier B and C markets arrive in batches, each one held back until its feed is confirmed and its bytecode review is complete. This tail is where an RWA-specific venue proves its worth, since these are precisely the assets that pooled designs heavily cap or decline entirely, and precisely the ones that thin generic pools misprice worst. See the [Listing framework](listing-framework.md).

## Stage 2: tokenized treasuries

At roughly $16B, tokenized US Treasuries are the largest transferable RWA category after stablecoins, so they are the obvious second class: a trader leaving equity exposure should be able to move into yield without leaving the venue.

Before the first treasury market can open:

* Robinhood Chain must have a treasury token, issued natively or bridged via CCIP, from a regulated issuer, freely transferable in the way USDY is rather than gated by an allowlist.
* A Chainlink feed on Robinhood Chain must track NAV and point at that exact token.
* The whole [listing checklist](listing-framework.md) must be met, together with the NAV addendum: NAV-type assets are quoted within a tight band around NAV, and the closed-regime logic defers to the fund's own accrual, because treasuries have no weekend gap of the kind equities do.

If a token restricts transfers in its contract, the vault is deployed in its permissioned form and allowlisted by the issuer, so that eligibility is enforced both by the issuer's hooks and by Iridius's registry.

## Stage 3: tokenized gold

PAXG-style gold trades continuously against a spot reference and has no sessions whatsoever, so it will be the simplest asset the venue ever lists: `OPEN` at all hours, with only feed cadence and inventory affecting the spread. The requirement is the same one that applies to every other asset, the exact token bridged to Robinhood Chain with its own Chainlink feed, plus a bridge review that covers the mint path, the custodian attestations and the bridge's pause behaviour.

## Stage 4: whatever the chain issues next

Robinhood has indicated that private-market tokens and other RWA classes are coming to this chain. Whatever arrives is held to the same checklist. An asset that cannot pass it (because the token has no feed of its own, there is no session signal, or the issuer's terms are not disclosed) will not be listed, no matter how loud the demand.

## What will not be listed

* Wrappers, vault shares or receipt tokens whose price would need to be calculated instead of fed. Pricing the exact token is non-negotiable; the July 2026 wGOOGLx episode is the case study, covered in [Lessons from RWA trading](../risk/lessons.md).
* Synthetics without a regulated issuer and without one-for-one backing.
* Any token whose bytecode review reveals transfer controls that were never disclosed.
