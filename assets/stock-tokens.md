---
description: What Robinhood Stock Tokens are, and which of their properties become important once they are traded.
---

# Stock Tokens

The launch catalogue is made up of Robinhood Stock Tokens. They are also the reason the venue sits on Robinhood Chain and not somewhere else: equities brought on-chain by a listed broker, with one-for-one backing, transferable as ordinary ERC-20s at all hours, and priced by Chainlink.

## What a Stock Token is

| Property          | Detail                                                                                                                                                       |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| Issuer            | Robinhood Assets (Jersey) Ltd                                                                                                                                |
| Legal nature      | Tokenized **debt securities**. The holder has a claim on the issuer that tracks the share, rather than owning the share.                                       |
| Backing           | One for one, against shares in US custody                                                                                                                    |
| Standard          | ERC-20 with 18 decimals, plus the ERC-8056 corporate-action interface on top                                                                                 |
| Availability      | Over 120 countries.                                                                                                                                           |
| Transfer controls | **None on-chain.** The documentation describes no allowlist, no transfer hook and no freeze function. Eligibility is applied in Robinhood's own interface and on the KYC'd primary issuance and redemption path. |
| Pricing           | Chainlink Data Feeds on a 24/5 schedule and Data Streams with market status; the price already includes the ERC-8056 multiplier                               |

## How these properties affect trading

* As **plain ERC-20s**, they can be held and transferred by vaults and RFQ settlement with no allowlisting, and an attested wallet can trade them without any per-token steps.
* Since **ERC-8056 absorbs dividends and splits**, value builds up inside the token and its feed. There is nothing to administer and no ex-dividend cliff to misprice. See [Corporate actions and dividends](../protocol/corporate-actions.md).
* **Chainlink prices that carry market status** underpins both anchor quoting and [trading regimes](../protocol/trading-regimes.md), because it delivers the mid and the session together through a single guarded source.
* **The absence of on-chain compliance** has two sides. These tokens can sit in any address, even one that Robinhood's own screening would reject. That is exactly why Iridius applies eligibility itself at the edge of the protocol, using the [eligibility registry](../architecture/eligibility.md), to every trader, LP and maker on every action that calls for it.

If Stock Tokens later adopted ERC-7943 (uRWA) or ERC-3643 hooks, nothing much would change: the vaults and settlement contracts already call `canTransfer` and `canReceive` defensively, and would only need the issuer to allowlist them. See [Asset roadmap](asset-roadmap.md).

## Issuer and freeze rights

Every Stock Token position carries two risks, and the venue discloses and prices both instead of ignoring them:

* **Credit risk on the issuer.** The token stands for debt owed by Robinhood Assets (Jersey) Ltd. If the issuer failed, the token's value would depend on what could be recovered from custody, not on the share's trading price. Vault LPs and token holders hold that claim alike.
* **Freeze rights.** Under the issuer's terms, tokens may be suspended, frozen or restricted in certain circumstances. The published documentation shows no on-chain freeze function. Before a market opens, its token's deployed bytecode is checked for pause, freeze, blacklist and forced-transfer roles, and the result appears on the listing page. Any such role that turns up is factored into the tier assignment and the caps.

See [Issuer risk](../risk/issuer.md).

## Current listings

Tier A is made up of tokenized SPY, QQQ, AAPL, MSFT and NVDA. The [Listing framework](listing-framework.md) sets out the complete tier assignment and the criteria a token must meet before its market can open.
