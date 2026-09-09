---
description: The source of a market's state, and how each state alters the way that market quotes.
---

# Trading regimes

Regular equity sessions add up to about 32 hours a week, while Stock Tokens trade without a break. The regime engine exists to price the gap between the two instead of pretending there isn't one.

## The four regimes

| Regime | Derived from | Quoting |
| --- | --- | --- |
| `OPEN` | Regular session, according to the oracle's market status | Tightest spreads, full-size clips |
| `EXTENDED` | Extended-hours session | Spreads ×1.5, clips ×0.75 |
| `CLOSED` | Underlying market closed | Spreads ×3.0, clips ×0.5 |
| `HALTED` | Stale oracle, `oraclePaused()`, corporate action, sequencer recovery or a guardian pause | No quoting; deposits and withdrawals continue |

The regime is held per market in `OracleRouter`, and each quote checks it. The multipliers are per-tier `ParamController` values.

## How the regime is determined

`OracleRouter` classifies the regime from two independent signals:

* the `marketStatus` field in Chainlink Data Streams, where 5 means closed under the v11 RWA schema, and
* the Data Feed's `updatedAt` timestamp, checked against the staleness bound for the current session (one hour in regular session, two in extended, and longer when closed).

Either signal can only make the regime stricter, never looser: a healthy-looking feed paired with a stream reporting closed results in a closed market. The transition function can be poked by anyone; the keeper service pokes it within seconds of each session boundary, and any swap that lands before the poke recomputes the regime inside its own transaction, so there is no window in which stale-session pricing could show up.

## Why a `CLOSED` market still gets quoted

Both alternatives are worse. Declining to quote at weekends reimposes market hours on an always-on chain and sends those users to custodial venues. Quoting weekends at weekday spreads turns LPs into the free counterparty for every Monday gap. The honest option sits in between: weekend liquidity is available, it costs more, it comes in smaller sizes, and the ticket shows precisely how much more. The closed-session multiplier is the price of gap risk, set conservatively at launch and recalibrated against observed Monday opens, with each change going through the timelock.

## A closer look at `HALTED`

A market halts if any of the following holds, and resumes once none does:

| Trigger | Cleared by |
| --- | --- |
| Feed stale beyond the session bound | A new oracle round arriving |
| Feed sets `oraclePaused()`, signalling a corporate action in progress | The feed resuming; see [Corporate actions](corporate-actions.md) |
| A single-round price move beyond the 25% guard | A timelocked review whose conclusion is published |
| The one-hour grace window following a sequencer outage | Expiry of that grace window; see [Sequencer and chain risk](../risk/sequencer-and-chain.md) |
| A guardian pause | An unpause accompanied by a published incident note |

A halt affects quoting only. LP deposits and withdrawals, RFQ cancellations and all view functions continue to work. The trade explorer shows the halt reason live, read from exactly the contract state the router uses.

## Calendar independence

The protocol contains no hard-coded market calendar. Holidays, half days and unscheduled closures all come through the oracle's market status, so the venue never quotes into a market the data reports as closed, and never stays shut when the data says it is open.
