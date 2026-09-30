---
description: The legal entities behind the protocol, and the company that operates the platform.
---

# Corporate structure

## Two entities behind one protocol

| Entity | Role |
| --- | --- |
| **Iridius Foundation** | Steward of the immutable contracts and of protocol governance. It holds the timelocked parameter multisig and the guardian until both pass to the governance module. |
| **Iridius Labs** (operating company) | Develops and operates the platform, the quote service, the indexer and the keeper software. It employs the team and holds any licence that is required. |

## Why the entities stay separate

* Non-custody and immutability are properties of the contracts, and housing them in a foundation shields protocol governance from the commercial pressures on the company that builds around it.
* The operating company is the one users meet, through a front-end, and it holds licences wherever they are required. Keeping it separate limits what the protocol itself is answerable for.
* If the operating company ceased to exist, the venue would carry on. Vaults would continue quoting from on-chain state, withdrawals would remain unconditional, and because the code is open source anyone could run the quote service, keeper, indexer and front-end. Convenience and the speed of new listings would suffer, but the market would not.

## Governance path

1. **Today:** parameters are changed by a timelocked multisig that the foundation holds, and each proposal is published along with its reasoning.
2. **Next:** control of parameters passes to a governance module. Everything that has to survive the handover does: the timelock, the public rationale, no access to funds, no power to gate withdrawals, and no means of interfering with any individual fill.
