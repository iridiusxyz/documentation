---
description: Where prices come from, and how the venue reacts when a market closes or a feed can no longer be trusted.
---

# Oracles and market sessions

There is a single path for prices into Iridius: the `OracleRouter` contract. It wraps Chainlink and runs the protocol's guards before any price can get close to a quote, a fill check or the regime engine. The anchor price supports the whole design, so no page here matters more than this one.

## Sources

| Source | Used for | Notes |
| --- | --- | --- |
| Chainlink **Data Feeds** (24/5 equities) | The mid used for vault quoting and band checks | Pushed on-chain, updated on deviation or heartbeat |
| Chainlink **Data Streams** (v11 RWA schema) | Session classification and sub-second cross-checks on RFQ fills | Pulled on demand, with `marketStatus` set to 5 when closed |
| Chainlink **Sequencer Uptime Feed** | The grace period following an outage | See [Sequencer and chain risk](sequencer-and-chain.md) |
| ERC-8056 `uiMultiplier()` | Showing holdings in share terms in the explorer | Taken from the token; the Chainlink price already reflects it |

Using feeds and streams side by side provides a cross-check: no fill can clear at a price that sits further from the other source than a configured tolerance, and the stream's market status can only ever make the regime stricter than the feed would on its own.

## Sessions

From the stream's `marketStatus` and the feed's `updatedAt` timestamp, `OracleRouter` labels every market **regular**, **extended** or **closed**. The [regime engine](../protocol/trading-regimes.md) then turns that label into spreads and clips.

| Session | Staleness bound | Effect |
| --- | --- | --- |
| Regular | 1 hour | The published tier spreads |
| Extended | 2 hours | Spreads ×1.5, clips ×0.75 |
| Closed | 4 days | Spreads ×3.0, clips ×0.5, with weekend quotes anchored to Friday's close |

Friday's close and Monday's open can sit a long way apart, and that gap is the whole reason for the closed-session multiplier. An LP quoting over a weekend prices that gap in the open. Someone trading at three on a Sunday morning pays a disclosed premium for a real service, not a hidden one for a service priced wrongly.

## Guards

`OracleRouter` rejects a price, or halts the market, before using it if:

| Condition | Action |
| --- | --- |
| The price is zero or negative | Revert |
| `updatedAt` is older than the session's staleness bound | The market is `HALTED` until a new round arrives |
| One round moves the price by more than 25% | The market is `HALTED` until a timelocked review publishes its finding |
| The feed reports `oraclePaused()` | The market is `HALTED` because a corporate action is in progress; see [Corporate actions](../protocol/corporate-actions.md) |
| Stream and feed differ by more than the tolerance | The fill reverts |

A halt touches only quoting and settlement in the affected market. Withdrawals, cancellations and reads carry on as normal the whole time.

## Only the exact token is priced

For each market, `OracleRouter` is set up to price the very token the vault holds. It never infers a price through a wrapper, a vault share or an exchange rate between tokens. This is what Edel Finance's wGOOGLx incident in July 2026 demonstrated: the oracle for the underlying was entirely correct, and the trusted value was a wrapper's exchange rate inflated 78 times. See [Lessons from RWA trading](lessons.md).

## Oracle changes

Oracle adapters for each market are held in `ParamController`, which makes swapping one a timelocked action that comes with a published rationale. `OracleRouter` exposes, per market, a view of the current feed, session, staleness, multiplier and pause flag, and the [trade explorer](../users/trade-explorer.md) shows it live. Any state the router enforces is, as a result, state the public can already see.
