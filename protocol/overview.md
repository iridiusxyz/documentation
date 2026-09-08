---
description: The path a swap takes from the first price request through to settlement.
---

# Protocol overview

Behind a single entry point sit three mechanisms: oracle-anchored vaults for everyday trade sizes, an RFQ lane for blocks, and a router that verifies the trader and then settles at whichever source gives the better result.

## The life of a swap

1. **Quote.** A user asks the price of 2,000 USDG of tokenized NVDA. The quote service mirrors the router's logic: it takes the guarded Chainlink mid from `OracleRouter`, checks the [regime](trading-regimes.md) and looks at [anchor vault](anchor-vaults.md) inventory, while also gathering signed [RFQ quotes](rfq.md) from makers when the size calls for it. The answer is one price, broken down into mid, spread and fee.
2. **Sign.** A single signature authorises the lot: a `SwapRouter` call with the swap parameters, a slippage bound, a deadline and a Permit2 pull of the input asset.
3. **Verify.** From here the chain is in charge. The router checks the `TRADER` attestation, recomputes the vault quote from oracle and vault state (or verifies the maker's EIP-712 signature), and makes sure the outcome falls within both the trader's limit and the protocol's oracle band.
4. **Settle.** All transfers happen at once: the input leaves the trader, the output leaves the vault or maker, and the fee goes to the `FeeCollector`. That is one transaction and one fill event, visible on Blockscout seconds later.

There is no book to maintain and nothing to deposit before or withdraw after. Custody lasts only as long as a single transaction, within contracts that cannot be changed.

## Multi-asset swaps

Every market quotes in USDG, so swapping one listed asset for another (tokenized AAPL to tokenized SPY, or a Stock Token to tokenized gold once gold is listed) runs as two legs via USDG within one atomic router call. The trader sees a single price, while the fill event records the two legs separately. If either leg cannot clear within its band, the entire swap reverts.

## Division of labour

| Size | Venue | Why |
| --- | --- | --- |
| Within the vault clip | Anchor vault | There is no counterparty to wait on, and a formula sets the spread |
| Beyond the clip | RFQ | A maker hedged on the underlying can price size more keenly than arithmetic |
| Any size where a maker wins | RFQ | The better price always settles; makers can undercut the vault at any time |

The clip does not impose a hard boundary. Makers can quote well below it and take retail flow any time their price beats the vault.

## Deliberate omissions

* **Cross-asset exposure is never pooled.** Each vault holds one asset plus USDG, and LPs pick their exposure market by market.
* **The protocol has no discretion when a fill happens.** Vault quotes are arithmetic on public state, and the RFQ path is a signature check. No one at Iridius has any control over an individual fill, in either direction.
* **Settlement is neither netted nor deferred.** Once included, a trade is final; nothing follows later.

## Reading order

The pages start at the core and work outwards. [Anchor vaults](anchor-vaults.md) covers inventory, [Pricing and spreads](pricing-and-spreads.md) the quote maths, [Trading regimes](trading-regimes.md) sessions and halts, [RFQ settlement](rfq.md) block trades, [Routing](routing.md) how the pieces connect, [Liquidity provision](liquidity-provision.md) the LP perspective, [Corporate actions](corporate-actions.md) the events, and [Fees](fees.md) the costs involved.
