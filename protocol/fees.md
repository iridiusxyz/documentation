---
description: What the protocol charges, who pays each charge, and what the money pays for.
---

# Fees

Every fee is charged at the point where value really moves, stated openly, and shown as a separate line on the ticket. There are no hidden markups, and no order flow is ever sold.

| Fee | Rate (initial) | Paid by | When |
| --- | --- | --- | --- |
| Vault swap fee | 2 bps of notional, included in the quoted all-in price but shown separately | Trader | On every fill |
| Vault spread share | 10% of the spread the vault realises | LPs, deducted from spread revenue | On every fill |
| RFQ settlement fee | 2 bps of notional | Trader | On every RFQ fill |
| Deposit and withdrawal | None | | |

A swap with two legs is charged on each leg. All rates are `ParamController` values that can move only through the timelock, always with a stated reason and never retroactively: a fill pays whatever fee applied at the moment it happened.

## The quote is also your invoice

Before anything is signed, each ticket shows mid, spread and fee as distinct numbers in bps and USDG, and the fill event writes that same breakdown on-chain. A fee change appears in the governance log before it can appear on any ticket.

## Where the fees end up

Fees collect in the `FeeCollector` contract and fund audits, the bug bounty, oracle and infrastructure costs, and the operation of the venue. Control over allocation moves to the governance module at the same time as parameter control. Details are in [Parameter governance](../transparency/governance.md).

## The economics as volume grows

Assume an average daily volume of 10M USDG and an average all-in half-spread of 12 bps:

* Swap and RFQ fees: 10M × 2 bps × 365, about 730K USDG per year.
* Spread share: 10M × 12 bps × 10% × 365, about 438K USDG per year.

That totals around 1.2M USDG a year, at volumes that are small next to the growth rate of this asset class. Running the same calculation at 50M a day gives roughly 5.8M USDG a year. More in [Business model](../resources/business-model.md).
