---
description: "A hands-on guide for professional makers: getting admitted, quoting, settling, and trading against vault rebalancing."
---

# Market maker guide

Makers supply the venue's professional liquidity. They handle block flow via [RFQ](../protocol/rfq.md), compete with the vaults wherever they can offer a better price, and earn through [skew pricing](../protocol/pricing-and-spreads.md) for keeping vault inventory close to target. Losing a quote costs nothing, and a winning one settles in one atomic step.

## Admission

To use RFQ you need a `MAKER` attestation, which professional trading firms get via the standard verification flow. At launch there are no quoting obligations, no membership fees and no volume commitments. Consistent quoting earns priority when requests are routed, and that is the only tiering anywhere.

On the operational side you need an EIP-712 signing key (an EOA or an EIP-1271 smart account), a standing Permit2 allowance from your settlement wallet to `RfqSettlement`, inventory in USDG and in each token you plan to quote, and a connection to the maker gateway.

## The loop

1. Open a connection to the gateway WebSocket ([protocol spec](../architecture/api.md)) and receive broadcast requests that carry pair, size, side and the taker's attestation class.
2. Price the request. When the regime is `OPEN` you hedge in the underlying within seconds; when it is `CLOSED` you are explicitly pricing weekend gap risk, which is precisely when your quote counts most and the vault quotes widest.
3. Sign and respond within the collection window of a few hundred milliseconds. The best quote wins, and at settlement the chain verifies your signature, your attestation, the nonce, the expiry and the oracle band.
4. Settlement takes `tokenOut` from your wallet and delivers `tokenIn` in one atomic step. If nothing fills, nothing moves.

Nonces handle bulk cancellation on-chain, and you can still cancel through the L1 delayed inbox if the sequencer is censoring. Keep expiries to a few seconds. The band ensures a stale quote reverts instead of filling at a bad price, but your hedging assumptions are still your own responsibility.

## The rebalancing trade

Vault skew is public: calling `AnchorVault.quote()` shows which side each vault is improving to draw corrective flow, up to the maximum skew term at the edge of the band. Trading against a skewed vault, within the clip, captures spread against the oracle mid without taking on risk, and this is the rebalancing mechanism doing its job, not a loophole someone discovered. Makers who do this systematically keep the vaults two-sided and earn the skew in return.

## On-chain checks on makers

| Check | Consequence |
| --- | --- |
| Signature, nonce, expiry | Standard practice; replays cannot succeed |
| A live `MAKER` attestation when the fill happens | Once the attestation is revoked, settlement stops immediately |
| The oracle band, on each fill | Beyond it, nobody can fill you and you can fill nobody |
| Atomicity | If the pull fails on either side, the whole fill unwinds |

The band cuts both ways by design: it limits what a stolen maker key can cost takers, and it limits what a stale quote can cost you.

## Reporting

The [trade explorer](trade-explorer.md) lists your fills alongside everyone else's, with the maker anonymised unless you opt otherwise. Authenticated API endpoints give you your own fill and quote-performance history, including win rate and price improvement against the vault, and routing priority is based on that last figure.
