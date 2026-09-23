---
description: How the contracts and services are secured, tested and monitored.
---

# Security programme

Much of the security comes from the design itself: immutable logic, a minimal privileged surface, and a band that limits how far nearly any failure can spread. The programme's role is to put pressure on the surface that is left and to keep watching it continuously.

## Audits

Before mainnet exposure is allowed to grow beyond the guarded caps, two independent audits take place. One firm concentrates on the economics and pricing logic (the spread formula, share accounting and band enforcement), and the other reviews the full contract set. Both reports are published without redaction. Any later change touching settlement logic ships as a new deployment with its own review; parameter changes are not reviewed in that way, and that is a large part of why behaviour lives in parameters in the first place.

## Invariant tests and fuzzing

The protocol's promises are encoded as machine-checked properties in the Foundry suite, which runs in CI on every commit and is fuzzed continuously:

| Invariant | Meaning |
| --- | --- |
| Band safety | No fill falls outside the oracle band, whatever state, regime or parameter set the fuzzer reaches |
| Share monotonicity | Value per share never decreases because of a swap, and mint and burn rounding always goes the vault's way |
| Withdrawal liveness | `withdraw` succeeds in every vault state, including `HALTED` and `RETIRED`, and also while paused |
| Inventory bounds | Quoting by itself can never push inventory beyond the band |
| Conservation | Value is conserved exactly across trader, vault or maker, and `FeeCollector` on every fill |
| Decimals | The 6-and-18 decimal split remains correct at both size extremes |

Oracle failures are tested as first-class scenarios instead of being assumed not to happen: for stale rounds, a paused feed, a stream disagreeing with its feed, 25% jumps and sequencer restarts, the expected halt behaviour is asserted in each case.

## Bug bounty

A public programme on an Immunefi-style platform starts at mainnet, and its top tier is kept for anything that breaks one of the invariants listed above. The quote service and the SDK are in scope, since a client that persuades a user to sign something harmful is a genuine attack even when the chain enforces correctly.

## Operational security

* The timelock multisig and guardian keys live on hardware, held by named signers at published thresholds. The guardian can only pause, and unpausing goes through the timelock, so a compromised guardian results in quoting being denied, not in losses.
* No service has a key that can touch funds. The only hot keys in the system are the makers' own; they bear their own key risk, and the band already caps their worst case.
* Alerts trigger on narrowing band margins, oracle divergence, saturated skew and unusual fill patterns. Each incident runbook starts with the same step, pause quoting, since a pause is guaranteed to cause no harm.

## Disclosure

Send vulnerability reports to security@iridius.xyz, which has a published PGP key. Every incident gets a public post-mortem within 14 days, and any resulting change goes through the timelock with that post-mortem cited as its rationale. For a venue like this, credibility means its [transparency commitments](../transparency/commitments.md) holding up on a bad day, and this page is where that is put to the test.
