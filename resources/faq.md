---
description: Straight answers to the questions traders, LPs and integrators really ask.
---

# FAQ

## Trading

**Does Iridius count as an exchange?**
It is a non-custodial swap venue: oracle-anchored vaults and competing makers supply the quotes, trades settle atomically on Robinhood Chain, and the venue never holds your assets. You have no account to fund and no book to rest orders on.

**Why is the spread wider tonight than it was this afternoon?**
The underlying market is closed. In the closed regime, quotes apply a ×3 spread multiplier and smaller clips, because LPs carry the gap risk until the next open. The regime badge tells you which multiplier is in force. See [Trading regimes](../protocol/trading-regimes.md).

**Can I still trade when a market is halted?**
No, and that applies to everyone, which is the whole point. Halts track the oracle (staleness, corporate actions, implausible jumps) and lift when it does. Throughout, your assets stay in your wallet.

**If I buy tokenized AAPL, am I an Apple shareholder?**
No. You hold a Stock Token, an ERC-20 debt security issued by Robinhood Assets (Jersey) Ltd that tracks the share, with dividends building up inside the token via its multiplier. Read [Stock Tokens](../assets/stock-tokens.md) before you trade in size.

**Why was my swap rejected before I had signed?**
Your wallet either lacks a live `TRADER` attestation, or your jurisdiction is restricted for that specific asset. The platform tells you which rule you failed. See [Eligibility registry](../architecture/eligibility.md).

**What happens to dividends, then?**
Into the token. Stock Tokens reinvest dividends by raising `uiMultiplier()`, and the price moves with it. You have nothing to claim. See [Corporate actions and dividends](../protocol/corporate-actions.md).

## Liquidity

**What does an LP actually earn?**
The spread realised on fills made by their vault, less the protocol's 10% share, which accrues as value per share. There are no emissions; the yield is simply what the market pays for immediacy. Each market's realised figures are visible to everyone in the [trade explorer](../users/trade-explorer.md).

**Can a halt or pause lock up my funds?**
No. Withdrawals function in every vault state, including halts and the guardian pause, and an invariant guarantees it. You receive a proportional, in-kind share of the vault's assets. See [Liquidity provision](../protocol/liquidity-provision.md).

**Does this amount to impermanent loss?**
Not as an AMM uses the term. Curve LPs lose to reference-price arbitrage, and anchored vaults do not leak value that way. You still have genuine inventory exposure (the token can go down) and gap risk on fills in the closed regime, and both are disclosed openly and paid for through the spread.

## The venue

**What is the source of the prices?**
From a formula applied to public state: the Chainlink mid, the tier spread, the regime multiplier, inventory skew and an itemised fee. No one at Iridius has the ability to change a single quote or fill. See [Pricing and spreads](../protocol/pricing-and-spreads.md).

**What happens if the company behind Iridius goes away?**
The contracts go on quoting from on-chain state, withdrawals remain unconditional, and the services are open source, so anyone can operate them. See [Corporate structure](../compliance/corporate-structure.md).

**How can I verify that execution is fair?**
Ideally, belief should not be required. The itemised breakdown of every fill is on-chain, and the venue publishes the distance of every fill from the reference price, unfiltered, at [Execution quality](../transparency/execution-quality.md).

**Can I integrate Iridius into my own app?**
Yes. You get a public [API and SDK](../architecture/api.md), a permissionless router and no partner tier. Eligibility is tied to your users' wallets, not to your application.
