---
description: A hands-on guide to swapping on Iridius, from connecting a wallet to understanding the result.
---

# Trader guide

## Before your first trade

1. **Wallet.** Use Robinhood Wallet, MetaMask or any standard wallet, or set up an embedded account secured by a passkey on the [platform](https://iridius.xyz/platform).
2. **Eligibility.** You go through the attestation flow a single time. The KYC provider verifies identity, sanctions status and jurisdiction, then issues a `TRADER` attestation to your wallet, and your personal data never reaches the chain. The platform always shows your current eligibility state. See [Eligibility registry](../architecture/eligibility.md).
3. **USDG.** Every market is priced in USDG. You can equally start from Stock Tokens already in your wallet, because token-to-token trades route through USDG without any extra step on your part.

## Understanding the ticket

Each quote has four lines:

| Line | What it is |
| --- | --- |
| Mid | The guarded Chainlink price underlying the quote, displayed with its oracle round |
| Spread | The price of immediacy on this venue, in bps and USDG, including inventory skew |
| Fee | The protocol's charge, shown separately |
| Regime badge | Whether the market is `OPEN`, `EXTENDED` or `CLOSED`, and the multiplier that follows |

A `CLOSED` badge means you are paying about three times the normal spread for weekend liquidity, along with a lower maximum size. That is the real cost of trading an asset whose underlying market is closed. If your trade can wait for Monday's open it will be cheaper, and the ticket is pointing that out.

## Slippage and expiry

Every transaction you sign includes a minimum-output bound and a deadline. If price or vault state moves beyond that bound before inclusion, the swap reverts instead of filling, which is rare given preconfirmations of around 100 ms. RFQ quotes also expire within seconds, and the platform automatically requests a new one.

## Large trades

Once you exceed the vault clip, the router prices your trade through [RFQ](../protocol/rfq.md) and nothing changes from your point of view: the ticket and breakdown look the same, and the venue line says "maker" in place of "vault". For now, working an order over time means splitting it manually, as execution algorithms are on the [roadmap](../roadmap/phases.md).

## Once you are filled

Your receipt sets the fill beside its on-chain event: the breakdown you were quoted, the venue that filled it, and a link to Blockscout. All fills on the venue, including yours, are aggregated against the oracle mid on the [execution quality](../transparency/execution-quality.md) page, so you never need to take execution on trust.

## What you own

A Stock Token is not the underlying share; it is a tokenized debt claim on Robinhood Assets (Jersey) Ltd that tracks that share. Dividends accumulate within the token via the ERC-8056 multiplier rather than being paid out in cash. Before trading in size, read [Stock Tokens](../assets/stock-tokens.md) and [Issuer risk](../risk/issuer.md) once; the disclosure shown at your first swap summarises both.

## If a market halts

A halt, whether caused by a stale oracle, a corporate action or sequencer recovery, stops quoting only in the market concerned. Your assets remain in your wallet and nothing is locked. The platform displays why the market halted and what state will lift the halt. See [Trading regimes](../protocol/trading-regimes.md).
