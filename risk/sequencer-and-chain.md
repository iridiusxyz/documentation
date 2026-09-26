---
description: How the venue responds when the chain underneath it misbehaves.
---

# Sequencer and chain risk

Robinhood Chain is an Arbitrum Nitro rollup, and Robinhood runs its only sequencer. That setup is normal for an L2 at this stage, and it brings specific ways of failing that a venue open around the clock must plan for rather than hope never happen.

## What can fail

| Scenario | Effect on users |
| --- | --- |
| Sequencer outage | No transactions are included on the L2 for the duration, while the underlying market keeps moving. |
| Sequencer censorship | Particular transactions are left out, such as an LP's withdrawal or a maker's cancellation. |
| Delayed L1 finality | Withdrawing to Ethereum still takes the canonical 7 days; trading on the L2 is unaffected. |
| Chain configuration change | A change to DA mode or the validator set, or enabling Timeboost or BoLD, would alter the assumptions that hold about finality and ordering. |

## Mitigations

### Post-outage trading grace period

`OracleRouter` reads the Chainlink L2 Sequencer Uptime Feed. When the feed shows the sequencer has only just recovered, all markets remain `HALTED` for a grace period of **one hour**. During the outage the underlying kept moving, LPs had no way to withdraw and parameters had no way to respond; the grace period lets oracle rounds update and participants respond before anyone can trade against a vault stuck mid-move. Withdrawals and cancellations are available the moment the sequencer recovers; only quoting has to wait.

### Every function is reachable via the L1 delayed inbox

Every state-changing function Iridius exposes can be called through Arbitrum's delayed inbox on Ethereum, and a transaction the sequencer has ignored for 24 hours can be forced through. In practice this means:

* a censoring sequencer cannot stop an LP from withdrawing,
* a censoring sequencer cannot stop a maker from cancelling quotes by nonce,
* the guardian's pause and the timelock's parameter changes cannot be censored either.

Forced inclusion does little for swaps, because a quote expires well before 24 hours pass, but nothing that keeps funds safe relies on the sequencer cooperating.

### Timestamps, not block numbers

On Arbitrum-stack chains, `block.number` returns a value derived from L1. For that reason quote expiries, staleness bounds, grace periods and the timelock rely solely on `block.timestamp`. When the protocol genuinely needs the L2 block height, it calls `ArbSys.arbBlockNumber()`.

### Calldata cost

The L1 data component of gas is priced by compressed calldata size. RFQ quote structs are tightly packed and signatures use the compact 64-byte encoding wherever possible, which keeps settlement below a cent even when blob prices spike.

## Monitoring the ordering policy

The sequencer currently orders first-come-first-served, with preconfirmations at around 100 ms. Iridius monitors both Timeboost (express-lane ordering) and BoLD (permissionless validation), since enabling either would change how quote competition and forced inclusion work. The design already assumes it will never receive favourable ordering, because the signed slippage limit and the oracle band bound every fill regardless of its position in a block, and any change to the configuration triggers a review that is published in the governance log.
