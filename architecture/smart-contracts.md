---
description: Each contract in the protocol, the one thing it does, and whether it can ever be changed.
---

# Smart contracts

The contract set is small on purpose. Each contract has one job, its logic is fixed for the life of a version, and everything adjustable sits in `ParamController` behind the timelock.

## The set

| Contract | Job | Mutability |
| --- | --- | --- |
| `SwapRouter` | The sole entry point for traders: eligibility, venue selection, two-leg assembly, and enforcing the band and the bound | Immutable |
| `AnchorVault` (one per market) | Holds inventory, quotes around the mid, and mints and burns LP shares | Immutable, with parameters in `ParamController` |
| `VaultFactory` | Deploys vaults from listed configurations | Immutable |
| `RfqSettlement` | Verifies maker EIP-712 quotes and settles them atomically | Immutable |
| `OracleRouter` | The single entry for prices: Chainlink adapters, guards, and session and regime state | Immutable, with adapters in `ParamController` |
| `EligibilityRegistry` | The single entry for permission, checking roles against attestations | Immutable, with policy adapters in `ParamController` |
| `ParamController` | Home of every tunable value, behind the timelock | Timelocked writes, public reads |
| `FeeCollector` | Collects protocol fees | Immutable |
| `Guardian` | Stops quoting and settlement during an incident | Can only halt; unable to move funds or block withdrawals |

## Interfaces for integrators

```solidity
// SwapRouter: the one call a trader makes
function swap(SwapParams calldata p) external returns (uint256 amountOut);
struct SwapParams {
    address tokenIn; address tokenOut;
    uint256 amountIn; uint256 minAmountOut;
    uint256 deadline;
    MakerQuote[] quotes;     // optional RFQ candidates, checked on-chain
    bytes permit;            // Permit2 pull for tokenIn
}

// AnchorVault: the LP side
function deposit(uint256 usdgAmount, uint256 tokenAmount, address receiver) external returns (uint256 shares);
function withdraw(uint256 shares, address receiver) external returns (uint256 usdgOut, uint256 tokenOut);
function quote(bool isBuy, uint256 amountIn) external view returns (uint256 amountOut, QuoteBreakdown memory b);

// OracleRouter: state anyone can read
function priceOf(address token) external view returns (uint256 mid, uint8 regime, uint64 updatedAt);
```

`QuoteBreakdown` splits out mid, spread, skew and fee, and every fill emits it, so the on-chain record contains exactly the decomposition the ticket displayed.

## Events

Each fill emits one event with the pair, the size, the venue that filled it, the full breakdown and the oracle round it used. Each parameter change emits one event with the old value, the new value and the timelock proposal hash. The [trade explorer](../users/trade-explorer.md) is built from these events alone and shows nothing sourced elsewhere.

## Upgrade philosophy

Proxies are not used. Shipping a new protocol version means deploying new contracts, with markets migrating as LPs decide to withdraw in kind and deposit again. The cost is less convenient migration; the benefit is a lasting guarantee that the code holding funds today is the code that was audited. The single exception is the adapter pattern in `OracleRouter` and `EligibilityRegistry`, where the set of accepted adapters is a timelocked parameter, because oracle products and attestation schemes change faster than settlement logic ever should.

## Access control at a glance

| Role | Held by | Powers |
| --- | --- | --- |
| Timelock proposer | Foundation multisig | Proposes parameter changes, each published together with its reasoning |
| Guardian | Foundation multisig, with a smaller quorum | Pauses; unpausing goes through the timelock |
| Keeper functions | No one, as they are permissionless | Pokes regimes and halts |
| Everything else | No one | There are no other privileged functions |
