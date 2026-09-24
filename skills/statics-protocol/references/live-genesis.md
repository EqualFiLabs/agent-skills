# Live Genesis

## Production boundary

The standalone Genesis system is live on Robinhood Chain production.

- Network: Robinhood Chain
- Chain ID: 4663
- Production launch: 2026-08-27
- Genesis Epoch ended: 2026-09-11 11:59:00 UTC
- Launch source commit: 43018f109006aa2c2eef2808adc2aa74dfc9a6d4

The broader multi-asset Statics protocol, Statics Dollar, and Morpho integration are not implied live by their presence in the repository.

## Production contracts

From deployments/robinhood-mainnet-genesis.json:

| Component | Address |
| --- | --- |
| STATICS | 0x2d8d6F4A93AcD7a916A5a654ec8b690bA3B3EAdd |
| Statics Operators / Genesis NFT | 0xad5E9F96A91D1A6F550580b157af2068A0e8F0BE |
| Genesis Vault | 0x8AAAF9a22f439589987B8f1e69d79ca4f648C297 |
| Activation Registry | 0xfC62e99CaE93878f83801f3d6Bb4f1762E720B30 |
| Fee Receiver | 0x4aa7237527A120c5aFD8Eb89718b57DaB3EDb6cc |
| Launch Distributor | 0xB6583f04e8C606e9F07E39e754Aba77B250Dc3FD |
| Treasury Vesting | 0xBcf0e357c359858aB166aEBb74f0E527aAb3d6ee |
| WETH | 0x0Bd7D308f8E1639FAb988df18A8011f41EAcAD73 |
| STATICS/WETH PoolId | 0xe79228d6cae086a58bf5b22220b454e5d1ca4f13da767ea5bbe032d5a1e82e8a |

Treat Fee Receiver activeDistributor() as authoritative for current rewards.

## Operator backing

The collection contains exactly 5,555 Operators.

Each Operator has a fixed gross STATICS backing claim of 180,000 STATICS.

Post-epoch, each circulating Operator also participates in the permanent native reserve mechanics defined by the Genesis Vault. The reserve denominator is fixed at 5,555.

The Vault tracks accounted reserveETH. Raw ETH balance is not reserve NAV.

## Buying an Operator

buyGenesis(tokenId, receiver) purchases a Vault-held Operator.

The STATICS price is fixed at 180,000 STATICS.

After the Genesis Epoch, required native value is:

```text
reserve buy-in + native acquisition fee
```

Read quoteGenesisPurchase() immediately before execution.

The purchase function accepts excess native value and refunds the difference.

## Redeeming an Operator

redeemGenesis(tokenId, receiver) returns:

```text
180,000 STATICS + current post-epoch reserve redemption payout
```

Read quoteGenesisRedemption() immediately before execution.

Redemption requires caller ownership and no active Genesis Secured Credit. The Operator returns to Vault inventory.

## Genesis Secured Credit

The live Vault supports secured credit against a caller-owned Operator.

Immutable mechanics:

- maximum current principal per Operator: 171,000 STATICS
- gross Operator STATICS backing: 180,000 STATICS
- maximum utilization: 95%
- service term: 30 days
- recovery grace: 1 hour after maturity
- fixed recovery residual: 9,000 STATICS
- no oracle liquidation
- no time-accruing STATICS interest

The Operator remains owned by the holder while credit is active, but owner-changing transfer and redemption are locked.

### Mutable launch defaults

The production launch manifest recorded:

- origination/draw service fee: 0.02 ETH
- extension service fee: 0.008 ETH
- recovery caller share: 20% of the fixed 9,000 STATICS recovery residual
- service-fee split initialized to 10% reserve / 90% treasury

Read current onchain values before quoting or executing.

### Credit actions

- openGenesisCredit(genesisId, principal)
- drawGenesisCredit(genesisId, amount)
- extendGenesisCredit(genesisId)
- repayGenesisCredit(genesisId, amount)
- recoverGenesisCredit(genesisId)

Opening, drawing, and extending use exact native service fees.

## Activation

Activation is permanent for the current owner until an owner-changing transfer resets the Operator to Tier 0.

The registry supports Tier 0 through Tier 4.

Launch-time per-step tier costs were:

| Step | STATICS cost |
| --- | ---: |
| Tier 0 -> 1 | 10,000 |
| Tier 1 -> 2 | 20,000 |
| Tier 2 -> 3 | 30,000 |
| Tier 3 -> 4 | 40,000 |

A direct jump pays every intermediate step.

Costs are governable. Always read tierCost(tier).

Launch-time multipliers:

- Tier 0: 1.00x
- Tier 1: 1.10x
- Tier 2: 1.15x
- Tier 3: 1.20x
- Tier 4: 1.25x

## Launch rewards

Operators must be registered to participate in the launch Genesis reward index.

registerGenesis(genesisId) is one Operator per call and requires caller ownership.

Registration follows the token through later transfers. On owner-changing transfer, previously earned rewards crystallize to the previous owner and activation resets to Tier 0.

claimAllGenesisRewards(genesisIds, receiver):

- accepts a caller-supplied list of caller-owned Operators;
- consumes both STATICS and WETH rewards for supplied Operators;
- also consumes crystallized prior-owner rewards;
- is caller-bounded and should be paginated for large portfolios;
- reverts if any supplied Operator is not currently owned by the caller.

## Transfer behavior

The collection is ERC-721 but not ERC-721 Enumerable.

Owner-changing transfer:

- resets activation to Tier 0;
- preserves reward registration on the token;
- crystallizes already attributed rewards to the prior owner;
- is blocked while Genesis Secured Credit is active.
