---
description: The eight rules any design decision in Iridius must pass before it ships.
---

# Design principles

## 1. The reference price is the truth

An RWA's true price is formed wherever its underlying trades, and this venue does not pretend that it is formed anywhere else. Quotes are constructed outward from the Chainlink mid, fills must stay within a limit measured against it, and [execution quality](../transparency/execution-quality.md) is measured against it and published. Working out what Apple is worth is someone else's task. The task here is getting that number on-chain at three in the morning and being honest about what it costs to do so.

## 2. Honest quotes

Each quote shows three labelled numbers, namely the mid, the spread and the fee the protocol takes. The session badge sits on the ticket itself rather than in a hover, so a trader sees "closed session, spreads widened" while making the decision, not afterwards while reconciling. There is no selling of order flow, no padding of prices and nothing buried in a curve. Any economic model that depends on its line items staying hidden is one this venue refuses to run.

## 3. Non-custodial, always

Iridius never takes possession of anything a user owns. Traders hold their own keys and sign for themselves; vault inventory belongs to LPs and sits in contracts that cannot be altered; an RFQ fill moves assets straight from maker to taker. An emergency pause exists, but it touches quoting alone, never funds, and never an LP's ability to withdraw.

## 4. Enforce compliance at the edge

A Stock Token contract places no limits on who can receive it, so each venue that lists one must settle that question for itself. Iridius settles it with attestations at every entry point, keeps identity data entirely off-chain, and tells an ineligible user why they cannot proceed before any signature is asked for, not once gas has been spent. See the [Compliance model](../compliance/model.md).

## 5. Treat the session as a first-class input

The risk of an equity token on a Tuesday afternoon differs from its risk at three on a Sunday morning, and the protocol prices that difference rather than ignoring it. The spread, the permitted clip size and whether the market quotes at all are each determined by the [trading regime](../protocol/trading-regimes.md), which takes its reading from the oracle's market-status field and not from anyone's guess.

## 6. Each market is isolated

Every listed asset has its own vault, with its own LPs, its own parameters and its own inventory. A problem in one market (a dislocation, a suspension, a delisting) has no route into any other. There are no shared pools and no socialised losses.

## 7. Everything is open to inspection

Every fill links through to its Blockscout transaction in a single click. Every parameter lives in a public contract that anyone can read. Every change to a parameter passes through a timelock and comes with a written rationale. The [trade explorer](../users/trade-explorer.md) exists so that "transparently on-chain" is something a sceptic can verify, not merely a claim.

## 8. Design for chain failure modes

A single sequencer can go down while global markets carry on trading. Feeds can stall. Corporate actions can knock price sources offline. For every one of these, the [Risk](../risk/framework.md) section already sets out the planned response (halt, widen, or sit through a grace period), since infrastructure that works only when everything else works does not deserve the name.

## What these principles imply

* No emissions pay for anything. Liquidity is funded out of spread the venue has genuinely earned, or it goes unfunded.
* No leverage, no margin, no liquidations. A single job, done well.
* No one steps in to set a price. Parameters change through the timelock, and quotes are produced by the formula.
