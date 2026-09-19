---
description: The whole system, on-chain and alongside it, and how trust divides between the two halves.
---

# System overview

Iridius comes down to a small set of immutable contracts on Robinhood Chain, with stateless services around them that exist only for convenience. One rule shapes the whole design: **the chain enforces, services only assist.** Remove any service and every guarantee remains; you lose convenience, but never funds and never fairness.

## The pieces

```
          Traders, LPs, Makers, Integrators
                        │
        ┌───────────────┼────────────────────┐
        │               │                    │
   Platform UI     Quote service        Maker gateways
   (Next.js)       (stateless)          (RFQ streaming)
        │               │                    │
        └───────────────┼────────────────────┘
                        │  signed transactions only
   ┌────────────────────▼─────────────────────────┐
   │              Robinhood Chain                  │
   │                                               │
   │  SwapRouter ── AnchorVaults ── RfqSettlement  │
   │       │              │               │        │
   │  EligibilityRegistry │        FeeCollector    │
   │       │         OracleRouter                  │
   │  ParamController (timelock)                   │
   └───────────────────────────────────────────────┘
                        │
        Indexer (Ponder / Envio) ── Trade explorer,
        execution-quality dashboards, keeper service
```

## On-chain

[Smart contracts](smart-contracts.md) lists each contract alongside its one responsibility. Across all of them, the following holds:

* **Logic is immutable; parameters are timelocked.** There are no upgrades to vault or settlement logic. Behaviour can change only via `ParamController` values sitting behind the timelock, each accompanied by a published rationale.
* **A single door for oracles.** Prices enter a contract only through `OracleRouter` and its guards; no other path reads a price.
* **A single door for eligibility.** The `EligibilityRegistry` alone decides whether an address may act; no contract makes that call on its own.
* **Pausing only ever stops things.** Quoting and settlement can be halted by the guardian. No one can block withdrawals, and no one can move funds.

## Off-chain

There are three services, and not one holds anything that can touch funds:

* **Quote service**: simulates router pricing and gathers maker quotes for the UI and the API. Because the router re-derives or re-verifies everything itself, the service has no way to change a fill. See [Off-chain services](off-chain-services.md).
* **Keeper service**: triggers regime transitions and halt conditions. The functions involved are permissionless, the reference implementation is open, and anyone may run a keeper.
* **Indexer**: turns chain events into the trade explorer and the execution-quality datasets. If it breaks it may show something wrong, but it cannot settle anything wrong.

## Trust map

| Party | Must be trusted for | Cannot do |
| --- | --- | --- |
| Chainlink | Correct prices and market status, within the guards | Move funds; the band and the halts limit the damage of a bad price |
| Robinhood (sequencer) | Ordering and liveness | Take funds; forced inclusion puts a ceiling on censorship |
| Robinhood Assets (issuer) | Stock Token terms and backing | Touch the venue's contracts; see [Issuer risk](../risk/issuer.md) |
| Attestation issuer | Making correct eligibility decisions | Price, settle or hold anything |
| Iridius Labs (services) | Uptime and convenience | Misprice a fill, take custody or block a withdrawal |
| Timelock multisig | Parameter changes, made publicly and after the delay | Move funds, block withdrawals or bypass the delay |

## Failure stance

Each component has a planned response to failure: oracles halt, the sequencer has a grace path, services fall back to calling contracts directly, and the band bounds parameters no matter what. The [Risk framework](../risk/framework.md) lists every one of them.
