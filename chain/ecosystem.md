---
description: The Robinhood Chain services and protocols Iridius connects to.
---

# Ecosystem integrations

Iridius is built to work with what Robinhood Chain already offers rather than recreating any of it. Every integration listed here lives either in the settlement path or in the supporting services.

## Chainlink

**Role:** pricing, market status, sequencer status, and a potential policy engine for eligibility.

Data Feeds and Data Streams deliver 24/5 equity prices that already include the ERC-8056 multiplier, together with the `marketStatus` field that drives the regime engine. The Sequencer Uptime Feed controls the grace period after an outage. Treasury and gold listings on the [Asset roadmap](../assets/asset-roadmap.md) are set to bridge via CCIP. If Chainlink ACE and CCID become generally available on Robinhood Chain, they are a candidate adapter for the eligibility registry. See [Oracles and market sessions](../risk/oracles.md).

## Paxos and USDG

**Role:** quote asset.

USDG is chain-native, MiCA-regulated and already the dollar underpinning the chain's lending markets, so it is what traders arrive with. Joint marketing with Paxos is part of the go-to-market plan. See [Quote asset: USDG](../assets/usdg.md).

## Uniswap, 1inch and other aggregators

**Role:** arbitrage flow, and distribution via aggregators.

Uniswap v2, v3 and v4 are live on the chain, as is 1inch. When arbitrageurs drag generic pools toward Iridius's anchored quotes, that is welcome flow, and skew pricing rewards them for rebalancing the vaults as they do it. Integration with aggregators through the public router interface is a distribution aim in its own right: routed flow goes wherever the anchored quote is best.

## Morpho Blue

**Role:** the chain's lending layer, and an obvious source of flow.

Morpho operates the chain's USDG lending markets, with roughly $80M to $90M of RWA deposits behind Robinhood Earn. Lenders and borrowers moving between yield and Stock Token exposure are precisely the flow a swap venue is there to serve.

## Providers of account abstraction

**Role:** gasless onboarding and batched transactions.

Both ERC-4337 entry points and EIP-7702 are supported. On the front-end, Alchemy Account Kit provides embedded accounts and gas sponsorship, and ZeroDev serves as the fallback. A first swap fits in one user operation, combining the Permit2 approval and the swap, with gas sponsored. Smart accounts use EIP-1271 to sign RFQ quotes.

## Robinhood Wallet

**Role:** the main wallet for traders.

The front-end deep-links into Robinhood Wallet, and a listing in its dApp directory is a distribution target, because the venue's first users are people self-custodying Stock Tokens outside the Robinhood app.

## Blockscout

**Role:** transaction transparency.

In the trade explorer, every fill, parameter change and fee accrual links to its transaction on Blockscout.

## TRM Labs

**Role:** sanctions and AML screening.

TRM operates at the sequencer level of the chain. Separately, the KYC provider that issues Iridius's eligibility attestations screens when an attestation is issued and again when it is renewed, giving two independent layers.

## Indexing

The trade explorer, the execution-quality dashboards and the keeper service run on either Ponder (TypeScript, self-hosted) or Envio HyperIndex on Robinhood Chain. See [Off-chain services](../architecture/off-chain-services.md).

## Arbitrum ecosystem

Robinhood Chain is an Arbitrum Orbit chain. The plan includes applying to the Arbitrum Foundation for audit subsidies and joining both the Arbitrum Open House 2026 buildathon track and Chainlink BUILD.
