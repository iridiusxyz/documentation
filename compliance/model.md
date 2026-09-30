---
description: The reasoning behind Iridius's compliance design and the mechanisms that enforce it.
---

# Compliance model

{% hint style="info" %}
This section explains how the protocol enforces compliance. It is not legal advice.
{% endhint %}

## Principle

The protocol does not take custody. Because the assets carry no on-chain restrictions of their own, compliance is enforced at the **protocol boundary**.

Stock Tokens are standard ERC-20s. Robinhood enforces eligibility in its own interface and via KYC'd issuance and redemption. On-chain, anyone's wallet can hold the tokens, so each venue that lists them must decide for itself who is allowed to trade. Iridius makes that decision at every entry point using the [eligibility registry](../architecture/eligibility.md), and it mirrors the issuer's jurisdiction list instead of drafting a looser one.

## Rules that are enforced

| Boundary | Rule |
| --- | --- |
| Swapping | A `TRADER` attestation: KYC'd, in a jurisdiction that is not restricted, following the Stock Token eligibility list |
| Providing liquidity | An `LP` attestation, limited to professional clients at launch |
| Making markets | A `MAKER` attestation, limited to professional trading firms |
| Vault share transfers | An `LP` attestation held by the recipient |
| Front-end access | Geo-fencing that blocks restricted jurisdictions |

Withdrawals are never gated. Owning shares is all an LP needs in order to exit, with no permission in between, because a compliance system that can trap funds has in effect turned into a custodian.

## How enforcement works

1. **Attestations instead of allowlists.** After identity, sanctions and residency checks succeed, the KYC provider issues an EAS attestation to the wallet with role, jurisdiction class and expiry, and no personal information.
2. **Ongoing re-screening.** Attestations expire and are renewed, and a revocation takes effect from the next action.
3. **Screening at two layers.** The KYC provider screens when attesting and when renewing, and separately the sequencer performs its own sanctions screening, with TRM Labs integrated at chain level.
4. **Modular policy.** The registry operates through adapters, so the policy engine (EAS, ONCHAINID, Chainlink ACE) and the set of accepted issuers can both be changed via the timelock with no redeployment. That lets rules tighten fast if a regulator or the issuer demands it.
5. **Permissionless reads.** All view functions remain open, letting explorers and aggregators index the protocol without restriction.

## Where the protocol does not intervene

* It does not hold user funds, other than during atomic settlement and in the LP-owned vaults.
* It performs no conversion into or out of fiat.
* It has no discretion over any individual fill. Quotes are arithmetic on public state, settlement is a signature check, and no one at Iridius can improve, worsen or block a given trade.

These properties keep the regulatory footprint narrow. The operating company is what users deal with through the front-end, and it is assessed for authorisation wherever that is required, while the protocol itself remains non-custodial, non-discretionary software.

## Privacy

Personal data stays with the KYC provider and is never written to the chain. Using the optional zero-knowledge credential route, a wallet can prove its eligibility without revealing which provider performed the check or the content of any underlying attribute.

## Alignment with the issuer's rules

Trader eligibility on Iridius follows the Stock Token eligibility list. If Robinhood tightens that list, the attestation issuer's rules tighten to match. If Stock Tokens later gain on-chain transfer hooks, either ERC-7943 or ERC-3643, the vaults and settlement contracts will honour them and be allowlisted in return. See [Asset roadmap](../assets/asset-roadmap.md).
