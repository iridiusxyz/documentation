---
description: The counterparty inside every tokenized security, and how the venue responds to it.
---

# Issuer risk

A Stock Token is not a share. What you hold is a tokenized debt security issued by Robinhood Assets (Jersey) Ltd, tracking a share held in custody in the US. Every position traded or held here carries that distinction with it, and the venue handles it as a risk of its own.

## What the risk looks like

| Component | Description |
| --- | --- |
| Credit risk | If the issuer became insolvent, holders would be creditors with a claim on the custodied shares, and they might recover only part of it, or recover it slowly. |
| Freeze and restriction | Under certain conditions the issuer's terms allow it to suspend, freeze or restrict tokens. If that right were used, vault inventory and trader holdings could be left unable to move. |
| Redemption terms | Redemption with the issuer requires KYC. The venue does not rely on redemption at all; it relies on secondary pricing via the oracle. |
| Regulatory action | The issuer could be compelled by a regulator to change its terms, leave jurisdictions or cease issuance. |

## The venue's response

### Disclosure comes first

The listing page for every market identifies the issuer, sets out what the token legally is, and states the result of the bytecode review. Before a trader's first swap or an LP's first deposit, each sees a plain-language account of the debt-claim structure. No venue can engineer issuer risk away, but a venue can make sure nobody holds it without knowing.

### Bytecode review before a market opens

According to the published documentation, Stock Tokens have no on-chain freeze function, and that statement alone is not sufficient. Before a market opens, the deployed bytecode of its token is checked for pause, freeze, blacklist and forced-transfer roles, and the result is posted on the listing page. If any such role is found, it is reflected in the tier assignment and the caps.

### Proof of reserve

When the issuer's custodied shares are covered by a Chainlink Proof-of-Reserve feed or an equivalent attestation, `OracleRouter` reads it and the listing page shows it next to the market. Where no such feed exists, the page states that plainly.

### Caps and a single issuer-concentration figure

Each market's exposure is limited by TVL and volume caps. Since every Stock Token has the same issuer behind it, the [trade explorer](../users/trade-explorer.md) also publishes, as one figure, the total vault inventory exposed to Robinhood Assets (Jersey) Ltd, so the concentration is declared openly instead of left for readers to assemble.

### Isolation

Markets share nothing, which means an issuer event hitting one token, or every Stock Token together, has no path to a market whose asset is bridged treasury or gold. Widening the set of issuers is a stated aim of the [Asset roadmap](../assets/asset-roadmap.md).

## What LPs and traders should know

A Stock Token, whether held in a wallet or through a vault share, is a claim on the custody arrangement of a regulated broker, not the underlying share itself. Should that issuer fail, the contracts go on working exactly as intended and in-kind withdrawals still function, but what you withdraw is the claim, and its price becomes a legal question instead of a market one. The venue's proposal for bounding this is caps and disclosure; anyone may reasonably take a more cautious position.
