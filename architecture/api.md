---
description: The public quote API, the TypeScript SDK and the protocol makers use to talk to the venue.
---

# API and SDK

Lacking an API, Iridius would be nothing more than a website. Having one makes it a backend: aggregators, wallets, agents and portfolio tools tap the same pricing the platform uses and settle through the same public router. There are no privileged endpoints and no partner tier. The reference front-end runs on precisely what this page documents, nothing more.

## Quote API (REST)

```
GET /v1/quote?tokenIn=USDG&tokenOut=AAPL&amountIn=2000000000
```

```json
{
  "amountOut": "9873400000000000000",
  "breakdown": {
    "mid": "202.41",
    "spreadBps": 10,
    "skewBps": -2,
    "feeBps": 2,
    "regime": "OPEN",
    "oracleRound": "0x…"
  },
  "venue": "vault",
  "rfq": { "quote": { "…": "…" }, "signature": "0x…" },
  "validUntil": 1788316201,
  "tx": { "to": "0xSwapRouter…", "data": "0x…" }
}
```

Each response itemises the breakdown, names the venue that priced best, includes any winning RFQ quote alongside the signature the chain will check, and supplies calldata ready for signing with the trader's bound already in place. A quote is only a preview: the chain re-derives or re-verifies every part of it, so the worst a bad quote can do to an integrator is cause a revert, never a bad fill.

Beyond that, the surface covers `/v1/markets` (listings, tiers, parameters, regimes), `/v1/fills` (paginated fill history including breakdowns), `/v1/execution-quality` (the data behind the [transparency page](../transparency/execution-quality.md)) and `/v1/eligibility/:address` (a preview of a role check).

## WebSocket

`wss://api.iridius.xyz/v1/stream` delivers fill events, regime changes and per-market quote updates at feed cadence. The platform's own tickets are fed by this very stream.

## TypeScript SDK

```ts
import { Iridius } from "@iridius/sdk";

const iridius = new Iridius({ chainId: 4663, signer });
const quote = await iridius.quote({ tokenIn: "USDG", tokenOut: "AAPL", amountIn: 2_000_000000n });
console.log(quote.breakdown);          // mid, spread, skew, fee, regime
const receipt = await iridius.swap(quote, { maxSlippageBps: 15 });
```

The SDK takes care of quoting, Permit2 signing, the eligibility preflight and decoding receipts, and it includes typed events for indexer consumers. Releases follow semver and come with a published changelog, so no breaking change ever lands without notice.

## Maker protocol

Makers connect over an authenticated WebSocket. They receive broadcast requests and reply with EIP-712 signatures:

```
→ { "type": "rfq", "id": "…", "tokenIn": "USDG", "tokenOut": "NVDA", "amountIn": "250000000000" }
← { "type": "quote", "id": "…", "quote": { …EIP-712 fields… }, "signature": "0x…" }
```

Getting admitted requires a `MAKER` attestation; the maker guide at [For market makers](../users/market-makers.md) covers the message schema, the signing domain and how to manage nonces. A lost auction carries no cost, launch comes with no quoting obligations, and consistent quoters climb the routing priority.

## API keys and rate limits

Anyone can call the quote endpoints, subject to per-IP rate limits; an authenticated key raises those limits and unlocks the WebSocket. A key exists solely so integrators can be identified for support and abuse control. It has no effect on pricing, and that is verifiable by anyone because vault pricing is a public contract read.

## Versioning

`/v1` is stable. Any deprecation is announced in the changelog no less than 90 days in advance, and the SDK pins its API version explicitly. The [Deployment](deployment.md) page lists contract addresses for each deployment.
