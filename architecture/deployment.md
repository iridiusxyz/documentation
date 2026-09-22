---
description: Where the contracts are deployed, how each deployment is verified and where the addresses are published.
---

# Deployment

{% hint style="warning" %}
There are exactly three sources for Iridius contract addresses: `contracts/deployments/4663.json` in the open-source repository, the registry linked from this page, and [iridius.xyz](https://iridius.xyz). Treat an address found anywhere else as hostile, and verify before you approve anything.
{% endhint %}

## Deployments

| Network | Chain ID | Status |
| --- | --- | --- |
| Robinhood Chain mainnet | 4663 | Production |
| Robinhood Chain testnet | Address published in the repository | Permanent staging, where each release is deployed first |

Testnet mirrors the mainnet parameters and runs against Robinhood's testnet Stock Tokens and faucet assets, so integrators can exercise the full path (attestation, quote, swap, withdrawal) with nothing at stake.

## Record of deployments

For every contract, `contracts/deployments/4663.json` records the address, the source commit, the compiler settings, the constructor arguments and the deployment transaction. Every contract is verified on Blockscout, and on each release a CI job checks that the on-chain bytecode matches the tagged source. As a result, "the audited code is the deployed code" is a claim you can verify yourself rather than one you have to take on trust.

## Release process

1. Tag the release, have the delta audited or reviewed, and publish the source.
2. Deploy to testnet, then run the invariant suite plus a scripted end-to-end pass against the live testnet oracles.
3. Deploy to mainnet via `CREATE2` so that addresses stay consistent across environments.
4. Configure parameters through the timelock instead of in constructors, so the governance log is complete from block one.
5. Update the deployment record, refresh verification, and only then announce.

Because the logic cannot be changed, any post-launch release takes one of two forms: a new market from the factory (parameters only, no new code), or a new protocol version deployed next to the old one, with LPs moving across whenever they choose, as described in [Smart contracts](smart-contracts.md). An old version is never shut off remotely, since no switch exists to do it.

## Configuration authenticity

Clients obtain addresses, the market list, tiers and oracle adapters by reading them on-chain from `ParamController` and the factory. The SDK has no hardcoded market list and reads the chain instead, which means a stale or tampered client configuration cannot steer users to the wrong markets without failing the signature and address checks.
