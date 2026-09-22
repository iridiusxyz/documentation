---
description: How the venue determines who may trade, supply liquidity and make markets, while keeping personal data off-chain.
---

# Eligibility registry

Stock Token contracts place no restrictions on transfers. Robinhood enforces eligibility in its own interface and at KYC'd issuance and redemption. Any venue that lists those tokens therefore has to enforce eligibility on its own, and Iridius does it at the **protocol boundary**: every swap, deposit, RFQ settlement and share transfer is checked.

## Roles

| Role | Who | Status |
| --- | --- | --- |
| `TRADER` | KYC'd individuals and entities outside restricted jurisdictions, in line with the Stock Token eligibility list | Live |
| `LP` | Liquidity providers, limited to professional clients at launch because of the inventory exposure a vault carries | Live |
| `MAKER` | Professional market-making firms admitted to RFQ | Live |
| `RELAYER` | Services that submit swaps on another party's behalf, such as integrators and account-abstraction bundlers | Live |

## Attestations

Eligibility is built on **Ethereum Attestation Service** attestations, compatible with ONCHAINID, which the KYC provider issues after completing identity, sanctions and residency checks.

Each attestation contains:

* the wallet address,
* the role,
* a jurisdiction class, with the actual country included only when the user chooses to disclose it,
* an investor class, where relevant,
* an expiry.

It holds no name, no document and no personal identifier. Attestations expire and are renewed by continuous re-screening, and a revocation takes effect at once.

### Zero-knowledge path

Through an optional credential route using Privado ID or zkPass, a wallet can prove "eligible in jurisdiction class X, investor class Y" without disclosing the provider that performed the check or any underlying attribute. Both forms are accepted by the registry.

## The check

```solidity
function isEligible(address account, bytes32 role) external view returns (bool);
```

`SwapRouter`, `AnchorVault` and `RfqSettlement` all call this for the relevant parties. If the check fails the call reverts, and because the front-end evaluates the same view before any signature is requested, an ineligible user sees the reason instead of spending gas to find out. All views remain permissionless, so explorers, indexers and aggregators can read anything they require.

### Actions subject to the check

| Action | Checked parties |
| --- | --- |
| `swap` | The trader (`TRADER`), plus the recipient if it is a different address |
| RFQ `settle` | Both the taker (`TRADER`) and the maker (`MAKER`) |
| Vault `deposit` | The depositor (`LP`) |
| Vault `withdraw` | Only ownership of the shares: leaving is a property, not a permission |
| Vault share transfer | The recipient (`LP`) |
| Relayed submission | The relayer (`RELAYER`), as well as the role of the underlying user |

## Adapters

The registry exposes an adapter interface, so the policy engine can be swapped out without changes to the core:

* **EAS adapter** at launch, which reads attestations by schema and issuer.
* **ONCHAINID adapter**, covering ERC-3643 identities.
* **Chainlink ACE / CCID adapter**, an alternative engine the timelock can enable if required.

The accepted issuers and schemas are held as a `ParamController` value, which makes tightening the policy or changing providers a logged, timelocked change rather than a redeployment.

## Permissioned assets

If Stock Tokens or bridged RWAs adopt ERC-7943 (uRWA) or ERC-3643 hooks, the vaults and `RfqSettlement` already make defensive `canTransfer` and `canReceive` calls and would in turn be allowlisted by the issuer. The token's rules and the venue's rules would then both hold, with neither party having to trust the other. See [Asset roadmap](../assets/asset-roadmap.md).

## Geo-fencing

Exclusion of restricted jurisdictions happens at several layers:

1. Wallets in restricted jurisdictions are never issued attestations.
2. Every wallet must self-attest its jurisdiction during onboarding.
3. IP-based geo-fencing against restricted jurisdictions is applied in the front-end.
4. The sequencer performs its own independent sanctions screening.

The attestation issuer maintains the restricted list, which follows the Stock Token issuer's own availability list; see the [Compliance model](../compliance/model.md).
