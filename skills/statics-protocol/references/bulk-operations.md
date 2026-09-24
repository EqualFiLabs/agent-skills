# Bulk Operator Operations

Use this workflow whenever a user owns or may own many Statics Operators.

## Goal

Turn one portfolio-level instruction into a safe, resumable sequence of live Genesis calls without requiring protocol-level batch methods for every action.

Examples:

- register all my Operators
- activate all of mine to Tier 3
- claim everything
- extend every credit expiring this week
- draw 25% of available credit from each, max 500,000 STATICS total
- repay all credit under 72 hours

## 1. Discover holdings

The Operator collection is not ERC-721 Enumerable.

Preferred methods:

1. connected wallet/NFT indexer;
2. indexed ERC-721 Transfer history, followed by ownerOf verification;
3. batched ownerOf(1..5555) calls as deterministic fallback.

Never execute from an unverified cached list.

## 2. Build a state table

Create one record per owned Operator with only fields needed for the user's intent.

Example:

```text
id | tier | registered | credit principal | maturity | recoverableAt | locked | action
```

## 3. Filter idempotently

Each action needs an eligibility predicate.

Examples:

```text
register all: owned && !registered
activate to Tier 3: owned && currentTier < 3
extend in 7 days: owned && credit.active && now <= maturity && maturity <= now + 7 days
full repayment: credit.active && principal > 0
```

Before retrying after failure, rebuild the relevant state and recalculate remaining work.

## 4. Calculate aggregate economics

Before execution, summarize:

- affected Operators
- skipped Operators and reason
- total STATICS pulled
- total native asset required
- total principal advanced or repaid
- approval changes
- expected transaction or batch count

Treat user limits as hard caps.

For budget-constrained operations, state the deterministic selection order, such as earliest maturity first or ascending Operator ID.

## 5. Choose execution method

Use, in order:

1. native Statics batch function;
2. owner-preserving account batch;
3. sequential transactions.

Current important native batch: claimAllGenesisRewards(genesisIds, receiver).

Do not use generic external multicall if target contracts would see the multicall contract as msg.sender for owner-sensitive actions.

## 6. Chunk by gas

For caller-bounded native batches:

1. choose a practical chunk;
2. simulate or estimate gas;
3. reduce if needed;
4. increase later chunks only with headroom;
5. re-verify ownership before submission.

Do not assume all Operators fit in one transaction.

## 7. Handle approvals safely

Aggregate exact or bounded STATICS spend for actions that pull tokens.

If allowance is insufficient, prefer one bounded approval covering the approved plan instead of unlimited approval.

## 8. Handle quote freshness

Post-epoch purchases should use:

```text
quote -> execute -> verify -> re-quote
```

Credit origination/draw and extension require exact current native fees.

Activation tier costs can change. Re-read them before execution.

## 9. Serialize dependent state transitions

Do not parallelize dependent state transitions simply because many Operators are involved.

Examples:

- multiple post-epoch buys change reserve state and future buy-in
- multiple redemptions change reserve state and future reserve payout

Independent actions such as registration or extension across different Operators may be suitable for owner-preserving account batching.

## 10. Verify every result

After each confirmed write or atomic batch:

- record transaction hash/receipt
- verify success
- re-read the state transition
- remove completed Operators from the remaining queue

Examples:

- registration -> registered(id) == true
- activation -> tierOf(id) == targetTier
- extension -> maturity increased by one term
- repayment -> principal decreased by exact amount
- full repayment -> credit inactive and lock cleared

Never mark a call completed from submission alone.

## 11. Resume safely

If execution stops partway:

1. do not restart from the original list;
2. rediscover ownership if transfers may have occurred;
3. rebuild eligibility;
4. recalculate remaining spend and approvals;
5. continue only remaining work.

## 12. Credit deadline policy

For requests like "make sure none are at risk":

1. classify active credits by maturity and recoverableAt;
2. prioritize already expired but not yet recoverable credits;
3. then nearest active maturities;
4. offer repayment or extension according to balance and preference;
5. never recover the user's own Operator as a safety action.

If credit is past maturity, extension is unavailable. Repayment remains available while credit is active.

## Suggested progress output

```text
Plan: 62 registrations
Completed: 40
Remaining: 22
Failed: 0
Skipped after recheck: 3 transferred/already registered
```
