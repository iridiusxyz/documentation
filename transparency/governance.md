---
description: How parameters change, who can change them, and where that authority ends.
---

# Parameter governance

Every adjustable setting in Iridius is a `ParamController` value, whether it is a spread, multiplier, clip, cap, band, fee, oracle adapter, eligibility issuer or tier assignment. Governance is the process that changes those values, and its scope was deliberately kept small. It can tune the venue, and it can do nothing else.

## The process

1. **Proposal.** The foundation multisig puts forward a change together with a written rationale stating what changes, why, and the supporting data, typically the [execution quality](execution-quality.md) record or a listing review.
2. **Timelock.** The proposal sits publicly for the timelock period: 48 hours for routine parameters, and 7 days for oracle adapters, eligibility issuers and fees. Everyone affected can see it coming, and an LP who disagrees can withdraw in advance with no strings attached.
3. **Execution.** The change is executed on-chain and emits the previous value, the new value and the proposal hash. The full parameter history of the venue lives in the governance log within the [trade explorer](../users/trade-explorer.md).

## Emergency procedure

Because a live incident cannot wait 48 hours, the guardian can pause quoting and settlement straight away. The safety lies in the asymmetry: a pause is immediate and does no harm, since withdrawals keep working, while unpausing and every substantive change must pass through the timelock. Every guardian action generates an incident note, and a pause that lacks one is a breach of the [commitments](commitments.md) in its own right.

## Limits of governance

| Cannot | Because |
| --- | --- |
| Move or freeze user funds | No contract contains such a function |
| Gate or delay withdrawals | Withdrawal is guarded by an invariant in every state |
| Change settlement logic | The logic is immutable; new logic requires a new deployment users choose to adopt |
| Apply fees retroactively | Fees are read when a fill happens and apply to that fill alone |
| Bypass the band | There is no way to override the band check |
| Act instantly on substance | Apart from the pause, everything goes through the timelock |

## Who governs

For now, the foundation multisig, with named signers and published thresholds (see [Corporate structure](../compliance/corporate-structure.md)). The planned governance module will inherit precisely these powers and precisely these prohibitions; the identity of the proposer may change, but the scope of a proposal never will. This is not a yield product. An Iridius parameter vote stakes nothing except the quality of the venue.
