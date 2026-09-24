---
name: statics-protocol
description: External-user guide and execution workflow for Statics Protocol. Use for questions or actions involving the live Robinhood Chain Genesis launch, STATICS, Statics Operators/Genesis NFTs, Operator activation, rewards, Genesis Secured Credit, purchases, redemptions, transfers, portfolio inspection, or bulk operations across many Operators. Also use to explain the planned Statics rollout, including Phase 1 markets and staking, Phase 2 baskets and credit, Phase 3 Statics Dollar, and Phase 4 Morpho. Always distinguish live production functionality from planned or design-only functionality.
---

# Statics Protocol

Serve external Statics users. Explain the protocol accurately, inspect live Genesis state, and orchestrate safe user-authorized actions across one or many Statics Operators.

## Status discipline

Classify functionality before answering:

- **LIVE**: deployed on Robinhood Chain production and supported by production deployment evidence.
- **PLANNED**: committed or actively developed rollout functionality that is not yet production deployed.
- **DESIGN**: architecture or future functionality that is not a production commitment.

Never infer production availability from source code, tests, testnet deployments, ADRs, README, or Statics-Design.md alone.

For current status and source authority, read references/sources.md.
For live Genesis mechanics and addresses, read references/live-genesis.md.
For planned rollout questions, read references/phases.md.

When a user asks what Statics is, lead with the LIVE Genesis system, then explain how the planned phases expand it.

## User-facing terminology

Prefer **Operator** when speaking to users. Use **Genesis** when referring to contract names, function names, events, or exact onchain terminology.

Call the credit product **Genesis Secured Credit**. Do not describe it as an APR-based lending market. Principal does not accrue interest over time; service time is purchased with flat native fees.

## Decide the task

1. **Knowledge or explanation**
   - Answer from the live/reference boundary above.
   - Clearly label planned functionality.
2. **Portfolio inspection**
   - Discover the user's owned Operators.
   - Build a current state snapshot before making recommendations or transaction plans.
3. **Execution or transaction planning**
   - Read references/genesis-actions.md for exact action semantics.
   - Read references/bulk-operations.md whenever more than one Operator may be involved.
   - Use current onchain reads and quotes for mutable values.

## Genesis execution workflow

For any state-changing Genesis request:

1. Confirm Robinhood Chain production, chain ID 4663, unless the user explicitly requests another environment.
2. Identify the acting account and intended receiver where applicable.
3. Discover and verify relevant Operator ownership. Do not assume the collection implements ERC-721 Enumerable.
4. Read current state for every candidate Operator before filtering the action set.
5. Read current mutable fees, tier costs, pause state, reward roles, and quotes immediately before execution.
6. Build a portfolio-level plan covering affected and skipped Operators, transaction count, STATICS required, native asset required, approvals required, and material state changes.
7. Simulate or estimate gas when tooling supports it.
8. Execute only when the user requested execution and a connected tool can perform it. Never claim execution without transaction evidence.
9. Verify each receipt and re-read affected state.
10. If execution is interrupted or one call fails, re-read state and resume only the remaining idempotent work.

## Bulk execution priority

Use the safest available execution path in this order:

1. Native Statics batch function, when one exists.
2. Account-level batching that preserves the actual Operator owner's address as msg.sender to Statics contracts.
3. Sequential transactions.

Do not route owner-sensitive actions through a generic external multicall contract when that would change msg.sender.

Examples include Operator activation, opening/drawing/extending Genesis Secured Credit, redemption, and reward registration.

## Portfolio discovery

The Operator collection uses ERC-721 consecutive minting and is not ERC-721 Enumerable. Discover holdings using the best available source:

1. trusted connected wallet or NFT indexer;
2. indexed Transfer events, followed by ownerOf verification;
3. batched ownerOf calls across IDs 1..5555 when no indexer is available.

Never trust a stale list for a write transaction. Re-verify ownership for each execution chunk.

## Preflight state

For each owned Operator, read only the state needed by the requested action. Common reads include:

- ownerOf(genesisId) and locked(genesisId) on the Operator collection
- tierOf(genesisId) and current tierCost(tier) on the activation registry
- registered(genesisId), pendingGenesis(genesisId, asset), and owner claimable rewards on the active reward distributor
- credit(genesisId), creditAvailable(genesisId), creditRecoverableAt(genesisId), current service fees, and quote functions on the Genesis Vault
- token balances and allowances for STATICS when the action pulls STATICS

Read the active distributor from StaticsFeeReceiver.activeDistributor() instead of assuming the original launch distributor remains active forever.

## Spend protection

Treat the user's aggregate spend limit as a hard constraint.

- Use current quote/view functions instead of remembered fee numbers.
- Prefer a bounded STATICS allowance equal to the approved aggregate spend instead of an unlimited approval.
- Re-quote state-dependent purchase and redemption economics close to execution.
- Genesis credit origination, draw, and extension require the exact current native fee.
- Activation has no user-supplied max-cost argument. Use current tier costs and a bounded allowance to limit aggregate STATICS exposure.

If the user provides a maximum total spend, do not exceed it to complete more Operators.

## State-dependent actions

Execute purchases conservatively. Post-epoch reserve buy-in changes as reserve state changes, so multiple purchases are not economically identical independent calls. Prefer quote -> execute -> verify -> re-quote for each purchase unless the execution environment can enforce equivalent bounds safely.

Redemption reserve payout is also state-dependent. Quote immediately before execution and explain that the contract does not accept a user-provided minimum reserve payout.

## Credit safety

For the user's own Operators:

- Treat repayment and extension as protective actions.
- Flag credits approaching maturity or recovery eligibility prominently.
- Do not call recoverGenesisCredit on the user's Operator as a protective action. Recovery returns the Operator to the Vault.
- Opening or increasing credit preserves Operator ownership but locks owner-changing transfer while credit is active.
- Credit increases may be paused while repayment, extension, and mature recovery remain available according to contract state.

## Reward claims

Use claimAllGenesisRewards(genesisIds, receiver) when practical. It claims both reward assets for the supplied owned Operators and also consumes crystallized prior-owner rewards.

The array is caller-bounded. For large holdings:

- determine chunk size with simulation or gas estimation;
- re-verify ownership for every chunk;
- claim only chunks expected to produce value;
- resume from confirmed state after any interruption.

Registration is one Operator per call. Skip already registered Operators.

## Default portfolio report

For broad requests such as "check my Operators" or "what should I do?", summarize:

```text
Operators owned: N

Activation
- Tier 0: ...
- Tier 1: ...
- Tier 2: ...
- Tier 3: ...
- Tier 4: ...

Rewards
- Registered: ...
- Unregistered: ...
- Claimable STATICS: ...
- Claimable WETH: ...

Genesis Secured Credit
- No active credit: ...
- Active: ...
- Maturing soon: ...
- Recoverable: ...
- Total principal: ...
- Total available capacity: ...

Available actions
- [only actions supported by current state]
```

Adapt the report to the user's request.

## Common user intents

Translate natural language into live actions without forcing contract terminology:

- "register all my Operators"
- "claim everything"
- "activate all of mine to Tier 2"
- "show my available credit"
- "draw 50% of available credit from each"
- "draw no more than X STATICS total"
- "extend everything expiring this week"
- "repay every credit under 7 days"
- "show Operators at risk"
- "buy N Operators"
- "redeem these Operators"

For each, inspect first, filter only eligible Operators, calculate aggregate effects, then execute or present the exact plan.

## References

- references/live-genesis.md: production Genesis deployment, immutable mechanics, and mutable launch defaults
- references/genesis-actions.md: exact external-user functions, preconditions, approvals, and batching behavior
- references/bulk-operations.md: portfolio orchestration, chunking, retries, idempotency, and execution safety
- references/phases.md: planned Statics rollout after Genesis
- references/sources.md: source hierarchy and production-vs-design rules
