# Planned Statics Rollout

This file describes planned functionality, not current production availability.

The phase structure is taken from the current Phase 1 release candidate work in EqualFiLabs/statics PR #96. Re-check production deployment evidence before changing any phase from PLANNED to LIVE.

## LIVE: Standalone Genesis

Production currently begins with:

- STATICS token
- 5,555 Statics Operators
- Genesis Vault and permanent native reserve
- activation tiers
- launch Genesis rewards
- permanent STATICS/WETH Doppler market and fee ingress
- Genesis Secured Credit

## PLANNED Phase 1: Markets and staking

Current release-candidate scope includes:

- permissionless Statics-hook general pools
- separate permissioned general pools
- creator-selected native Uniswap v4 LP fees
- Statics public hook fees and protocol-owned liquidity on the public path
- creator and treasury revenue accounting
- PositionNFT
- global STATICS staking
- reward selection and reward-restriction policy
- creator-operated permissioned venue controllers
- approved permissioned LP infrastructure

The standalone Genesis deployment remains separate and untouched by Phase 1 deployment.

## PLANNED Phase 2: Baskets, basket credit, and Genesis integration

Planned Phase 2 expands the same StaticsDiamond with:

- fixed-composition static baskets
- basket minting and redemption
- basket collateral and rewards
- self-backed basket credit
- flash-loan composition
- canonical basket liquidity
- borrow-to-liquidity flows
- Diamond-side Genesis integration and Position linkage
- planned permissioned basket and restricted-asset adapter infrastructure

A BasketToken represents fixed underlying amounts. Basket composition does not rebalance.

## PLANNED Phase 3: Statics Dollar

Planned capabilities include:

- USDstx senior token
- volatile collateral profiles
- ERC-1155 Risk Shares
- pegged collateral profiles
- recombination
- series lifecycle and transition
- insurance and recovery
- Risk Share liquidity and pairing flows

## PLANNED Phase 4: Morpho integration

Planned Phase 4 adds Morpho integration around Position-owned finance, including:

- market administration
- Position collateral deployment
- borrowing and repayment
- synchronization and settlement
- recovery and views

## Roadmap wording

Say:

```text
Today, the live Statics product is the standalone Genesis system.
Phase 1 is planned to add general Statics markets, PositionNFTs, and STATICS staking.
Phase 2 is planned to add baskets and basket-backed credit.
Phase 3 is planned to add the Statics Dollar.
Phase 4 is planned to add Morpho integration.
```

Avoid launch dates unless a current official source explicitly commits to them.
