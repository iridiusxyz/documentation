---
description: The concrete promises this venue makes, each with a way to verify it.
---

# Commitments

Claiming transparency costs nothing; making commitments does. Every promise on this page is paired with a way to verify it. If any of them fails that check, it is an incident and is treated as one.

## Pricing

* **Each fill is broken down on-chain.** Mid, spread, skew and fee are all in the fill event. Check: compare any fill on Blockscout with its receipt.
* **Fills never land outside the band.** The contract enforces this as an invariant; it is not a policy someone applies. Check: the invariant suite is public, and the explorer shows the band margin for every fill.
* **Execution quality is published in full, with no filtering.** The distance between each fill and the oracle mid, aggregated daily and available raw for download. Check: [Execution quality](execution-quality.md), which can be recomputed from events.
* **Order flow is never paid for, and no routing is privileged.** On every fill, the router settles at the best price it can verify on-chain. Check: reconstruct the venue comparison for any fill from the oracle round and vault state recorded in its event.

## Funds

* **Funds are never held in custody.** Settlement is atomic, and LPs own the vault inventory. Check: the audits confirm that no contract function other than settlement and withdrawal moves user assets.
* **Withdrawals carry no conditions.** Whatever state the vault is in, and whether or not anything is paused. Check: the invariant suite, plus the withdrawals made during halts, which appear in the explorer like any others.

## Change control

* **Every parameter change sits behind a timelock, with its reasoning published ahead of execution.** Check: the governance log records proposal, rationale, window and execution for the venue's whole history.
* **Fees never apply retroactively.** You pay the fee that applied when you filled. Check: each fill event records the fee in force.
* **A live deployment's settlement logic is never altered.** Any new logic comes as a new deployment, which users choose to adopt. Check: bytecode is verified against tagged source, asserted in CI, and anyone can reproduce it.

## Disclosure

* **Every listing comes with its evidence.** For every market, the tier assignment data, bytecode review results and issuer disclosures are published before it opens. Check: the listing record in the explorer.
* **Every incident receives a public post-mortem within 14 days.** Check: the incident log, where any incident without a post-mortem is a breach in itself.
* **Risks are stated plainly in these documents.** Issuer, gap and oracle risk are placed in every participant's reading path, not tucked into an appendix. Check: read [Risk](../risk/framework.md).

## Promises we will not make

We do not promise best execution against every venue in existence, profits on anyone's position, or uptime for convenience services. The promises above are the ones the design can truly hold itself to, and that is precisely why they are the ones we make.
