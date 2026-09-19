---
description: The stateless services that sit around the contracts, and why none of them needs to be trusted.
---

# Off-chain services

The contracts are surrounded by three services, all bound by a single constraint: a service can make the venue faster or more convenient, but never more trusted. If any of them fails, convenience suffers and the guarantees do not move at all.

## Quote service

The quote service exists to answer one question: what would this swap cost right now? It simulates `SwapRouter` against live oracle and vault state, sends RFQ requests out to connected makers, gathers their signatures over a few hundred milliseconds, and returns the best composite quote complete with its breakdown. The platform ticket and the public [API](api.md) both sit on top of it.

The reasons it needs no trust:

* It holds no keys and no funds. What it produces is a preview and, for RFQ, a bundle of maker signatures that the chain verifies independently.
* At fill time the router re-derives vault pricing on-chain, so an incorrect preview ends in a revert against the trader's signed bound rather than a bad fill.
* The vault path cannot be hidden: if the vault beats every quote supplied, the router settles through the vault.

With the service offline, vault swaps continue from any interface able to reach the contract, at the same prices. Only RFQ aggregation and convenience are lost.

## Keeper service

Staleness halts, regime transitions and the expiry of sequencer grace are all permissionless pokes; the keeper service is just the process that calls them on time. Each quote recomputes the regime inside its own transaction regardless, so a slow keeper cannot produce stale-session pricing, only a delay before the explorer's display updates. The reference keeper is open source, and anyone may run one.

## Indexer

Either Ponder (self-hosted, TypeScript) or Envio HyperIndex converts fill and parameter events on Robinhood Chain into the [trade explorer](../users/trade-explorer.md), the [execution quality](../transparency/execution-quality.md) dataset and per-market LP statistics. Each figure shown links to the event it came from, so someone who distrusts the indexer can reconstruct all of it from Blockscout.

## Maker gateways

Makers connect to the quote service using the WebSocket protocol described in [API and SDK](api.md): requests go out as broadcasts and signed quotes return. The makers operate those gateways themselves, and Iridius only relays and ranks. When a maker's connection drops, its quotes leave the auction and nothing else is affected.

## Monitoring

The operations stack watches only public state, the same state anyone can read: oracle staleness and divergence, vault skew relative to bands, halt states, sequencer uptime, how near fills land to the band, and drift in execution quality. Alerts go to the on-call guardian. There is no privileged telemetry, since everything worth watching is already on-chain; that is a design statement as much as an operational one.

## Running them yourself

All of the above is open source. If you want independence, you can run the quote service, keeper and indexer from the public repositories against any RPC provider you like, and the [platform](platform.md) front-end serves as a reference client for the same APIs it documents.
