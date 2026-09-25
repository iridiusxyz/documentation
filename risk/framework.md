---
description: Every material risk the venue carries, alongside who bears it and what contains it.
---

# Risk framework

Assets from the real world arrive carrying risks from the real world. Listing those risks, naming who holds each of them and recording what limits it is the only honest response. This section is that list. If something belongs here and is missing, treat it as a documentation bug and please tell us.

## The map

| Risk | Borne by | Mitigation | Detail |
| --- | --- | --- | --- |
| Oracle failure or manipulation | LPs, traders | Guards, staleness limits, cross-checks, the band, halts | [Oracles and market sessions](oracles.md) |
| Weekend and overnight gaps | LPs | Spread multipliers and smaller clips while the market is closed | [Trading regimes](../protocol/trading-regimes.md) |
| Issuer credit and freeze rights | Everyone holding a Stock Token | Disclosure, review of bytecode, tier caps, one published figure for single-issuer exposure | [Issuer risk](issuer.md) |
| Sequencer outage or censorship | LPs, and traders caught partway through | A grace period on recovery, forced inclusion through L1, and halts | [Sequencer and chain risk](sequencer-and-chain.md) |
| Contract bugs | Everyone | Audits, invariants, a bounty, guarded caps, very little upgradeability | [Security programme](../architecture/security.md) |
| Adverse or toxic flow | LPs | Inventory bands, skew-aware pricing, one-sided quotes and clips | [Anchor vaults](../protocol/anchor-vaults.md) |
| Quote-asset depeg | Everyone | A native issuer under MiCA regulation; disclosed as a concentration | [Quote asset: USDG](../assets/usdg.md) |
| Parameter error | LPs, traders | Timelock, a published rationale, the band as a backstop, guarded caps | [Parameter governance](../transparency/governance.md) |
| Maker misbehaviour | Takers | Signature checks, expiry, nonces, the band, revocable attestations | [RFQ settlement](../protocol/rfq.md) |
| Regulatory change | The venue | A boundary a parameter can tighten, with access gated by jurisdiction | [Compliance model](../compliance/model.md) |

## Where most failures end up

The oracle band is where most of the failures listed above come to a stop. A badly set parameter, a stolen maker key or a quote sent down the wrong route are all upstream problems, and in every case no fill can land further from the last guarded oracle price than the band allows. That turns a long tail of scenarios into a bounded cost per fill, and it is the reason the guarded launch caps mean anything at all: the worst case is a figure you can work out, not a figure you have to hope for.

## Risks not covered

Being explicit about what remains is part of the framework too:

* **Market risk.** Buying tokenized NVDA gives you NVDA exposure. The venue prices transactions; it does not insure positions.
* **Issuer insolvency.** Disclosure and caps make this exposure visible and put a limit on it. Neither one removes it.
* **An accurate oracle reporting a violent market.** Halts fire on staleness, on pauses and on implausible jumps, never on real volatility. When the underlying crashes, the crash comes through here exactly as it does there.

## How this section stays up to date

Any incident that falls somewhere on the map above receives a public post-mortem and, when the evidence justifies it, a change to a parameter or to the design through the timelock. [Lessons from RWA trading](lessons.md) gathers incidents from across the industry together with this venue's response to each; Iridius aims to hold its own record to the same standard.
