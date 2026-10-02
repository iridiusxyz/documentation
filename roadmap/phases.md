---
description: What is running today, what is capped, and the sequence in which the venue expands.
---

# Live today and coming next

The venue grows in phases, and each gate opens on a published criterion, not on a calendar date. Features ship and caps rise when the record justifies them. Treat any dates below as targets; the gates are the real commitments.

## Phase 0: testnet (complete)

The full stack ran on Robinhood Chain testnet using Robinhood's testnet Stock Tokens and faucet: vaults, RFQ, router, eligibility and explorer. That deployment remains as staging, and every release passes through it. See [Deployment](../architecture/deployment.md).

## Phase 1: guarded mainnet (current)

* Tier A markets, namely tokenized SPY, QQQ, AAPL, MSFT and NVDA, all quoted in USDG.
* Anchor vaults running on launch parameters, within the per-market TVL and daily volume caps defined in the [listing framework](../assets/listing-framework.md).
* RFQ operating with founding makers, and attestations being issued to traders, LPs and makers.
* The trade explorer and the [execution quality](../transparency/execution-quality.md) dataset publishing from the very first fill.

**Gate to Phase 2:** publication of two clean audits, 60 days of execution-quality history in which median `OPEN` fills stay within their tier targets, and zero band or withdrawal invariant events.

## Phase 2: a wider catalogue and larger caps

* Tier B and C Stock Token markets added in batches as feeds arrive and bytecode reviews complete, since the long tail is the whole point.
* Cap increases via the timelock, each one citing the record built up to that point.
* LP onboarding opening beyond the founding group, one jurisdiction at a time as [compliance](../compliance/model.md) work is completed.
* Completed portfolio surfaces: cost basis, display in share terms, LP analytics and CSV export.

**Gate to Phase 3:** vaults sustaining two-sided performance across tiers through at least one high-volatility week and one long weekend, without any manual intervention.

## Phase 3: infrastructure

* The public [API and SDK](../architecture/api.md) leaving beta, with aggregator and wallet integrations, and agentic order flow treated as a first-class integrator in view of the chain's direction.
* The first cross-chain listings, in line with the [asset roadmap](../assets/asset-roadmap.md): treasuries and then gold, via CCIP, each passing the complete listing checklist.
* Execution algorithms for working size over time, specifically TWAP on the vault path.

## Phase 4: governance handover

Control of parameters passes from the foundation multisig to the governance module, keeping the timelock, the published rationale and the [prohibitions](../transparency/governance.md) exactly as they are. The proposer changes; the limits on what a proposal can do do not.

## Intentionally left off this roadmap

No leverage, margin, perpetuals or lending. Adjacent products are for other protocols. This venue aims to be the place where RWAs trade properly, and every phase above is devoted to that and nothing else.
