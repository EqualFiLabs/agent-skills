# Genesis Actions

Use this as the external-user action map for the live standalone Genesis deployment.

Mutable values must be read onchain immediately before execution.

## Inspect portfolio

Read only what the requested action needs:

- Operator collection: ownerOf, locked
- Activation Registry: tierOf, multiplierBps, tierCost
- Fee Receiver: activeDistributor
- Active distributor: registered, pendingGenesis, ownerClaimable
- Genesis Vault: credit, creditAvailable, creditRecoverableAt, service fee and quote functions
- STATICS: balanceOf, allowance

## Buy Operator

Function: GenesisVault.buyGenesis(tokenId, receiver)

Preflight:

- isVaultInventory(tokenId)
- quoteGenesisPurchase()
- current STATICS balance and allowance

Bulk behavior: state-dependent. Prefer serial quote -> buy -> verify -> re-quote.

## Redeem Operator

Function: GenesisVault.redeemGenesis(tokenId, receiver)

Preflight:

- ownerOf(tokenId)
- creditActive(tokenId)
- locked(tokenId)
- quoteGenesisRedemption()

Bulk behavior: no native batch. Re-quote near each redemption.

## Activate Operator

Function: ActivationRegistry.activate(genesisId, targetTier)

Preflight:

- ownerOf(genesisId)
- tierOf(genesisId)
- tierCost for every intermediate tier
- STATICS balance and allowance

Cost:

```text
sum(tierCost(currentTier + 1) ... tierCost(targetTier))
```

Do not treat tierCost(targetTier) as the complete cost of a multi-tier jump.

Bulk behavior: no native batch. Skip Operators already at or above target.

## Register Operator for Genesis rewards

Function: active reward distributor registerGenesis(genesisId)

Preflight:

- Fee Receiver activeDistributor()
- distributor registered(genesisId)
- ownerOf(genesisId)

Bulk behavior: no native batch registration. Skip already registered Operators.

## Claim Genesis rewards

Preferred function: claimAllGenesisRewards(genesisIds, receiver)

Every supplied ID must currently be owned by the caller.

Re-verify ownerOf for every ID in the chunk.

The function claims both reward assets for supplied Operators and crystallized prior-owner rewards.

Use simulation or gas estimation to choose chunks.

## Open Genesis Secured Credit

Function: GenesisVault.openGenesisCredit(genesisId, principal)

Preflight:

- ownerOf
- creditActive
- creditLimit
- creditAvailable
- creditIncreasesPaused
- quoteGenesisCredit(principal)

Exact current native origination fee is required.

Bulk behavior: no native batch.

## Draw additional credit

Function: GenesisVault.drawGenesisCredit(genesisId, amount)

Preflight:

- credit(genesisId)
- creditAvailable(genesisId)
- current time vs maturity
- current service-fee quote

Draw increases principal but does not extend maturity.

## Extend credit

Function: GenesisVault.extendGenesisCredit(genesisId)

Preflight:

- credit(genesisId)
- quoteGenesisCreditExtension(genesisId)

At block.timestamp == maturity, extension is still allowed. At maturity + 1, it is not.

Extension adds exactly one 30-day term and does not change principal.

## Repay credit

Function: GenesisVault.repayGenesisCredit(genesisId, amount)

Repayment is permissionless for the payer. The payer does not need to own the Operator.

Preflight:

- credit(genesisId)
- STATICS balance and allowance

Full repayment closes credit and removes the credit lock.

## Recover expired credit

Function: GenesisVault.recoverGenesisCredit(genesisId)

Preflight:

- quoteGenesisCreditRecovery(genesisId)
- creditRecoverableAt(genesisId)
- verify current time

Recovery is permissionless only after current time is strictly later than recoverableAt.

Do not use recovery as a protective action on the user's own Operator. Prefer repay or extend when available.

## Transfer Operator

Before transfer:

- verify locked(genesisId) == false
- explain activation resets to Tier 0
- explain registration follows the token while already earned rewards crystallize to the prior owner

Active Genesis Secured Credit blocks transfer.
