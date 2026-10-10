# Statics phased rollout

This guide presents standalone Genesis and Phase 1 as the live user experience. Phase 1 production addresses are supplied in [phase-one-deployment.md](phase-one-deployment.md) after release. The later Diamond cuts are implemented in Statics master but require separate production activation. Check [sources.md](sources.md) for the evidence needed to answer a current deployment question.

## Live standalone Genesis

The production Genesis manifest records the STATICS token, 5,555 Operators, their fully backed vault and native reserve, activation tiers, launch reward distributor, permanent STATICS/WETH Doppler market, and Genesis Secured Credit. The standalone contracts and market are separate from the later Diamond. See [live-genesis.md](live-genesis.md) for addresses and mechanics; verify mutable state onchain.

## Live Phase 1: markets, PositionNFTs, and staking

Phase 1 uses a `StaticsDiamond` with public and permissioned Uniswap v4 market paths, PositionNFT accounts, global STATICS staking and selected-asset rewards, managed public LP positions, range gauges, weekly reserve-backed protocol incentives, direct funded reward slots, typed batch claims, market views, and guardian stops. Public pools support compatible ERC-20 pairs and native ETH/ERC-20 pairs. Permissioned venues have their own hook, controller, trusted periphery, approved non-transferable LP positions, and fee policy. See [phase-one.md](phase-one.md) for actions and boundaries.

The release configuration starts with a zero general-pool creation fee, which keeps creation owner-curated. A nonzero fee enables permissionless public-pool creation under the contract's creator-authorization rules. Read the current fee before describing who may create a pool. Creation does not seed liquidity automatically.

Phase 1 does not install basket, self-backed credit, flash-loan, Diamond-side Genesis integration, Statics Dollar, Morpho, or borrow-to-liquidity selectors. Its gauge allocation uses raw staked STATICS, not Operator activation multipliers.

## Phase 2: baskets, credit, flash composition, and Genesis integration

The next timelocked cut adds fixed-composition BasketTokens, basket creation and redemption, basket collateral and rewards, proportional self-backed credit, canonical basket pools and liquidity, borrow-to-liquidity, basket-vector and single-asset flash loans, and Diamond-side Genesis Operator linkage and rewards. Existing standalone Genesis contracts are not redeployed by this cut. A separate governance handoff binds the new integration surface.

Each BasketToken represents a fixed constituent bundle; its composition does not automatically rebalance. A basket's creation and liquidity configuration remain distinct from general-pool creation.

## Phase 3: Statics Dollar

The next cut adds Dollar periphery and a separate `StaticsDollarCoreDiamond` custody and solvency boundary, plus the senior `USDstx` token and ERC-1155 Risk Shares. The implemented design includes volatile and pegged collateral profiles, issuance, recombination, health controls, insurance, transition, and recovery. Dollar Core and the ordinary user Diamond have different roles even though they share timelock governance.

## Phase 4: Morpho

The final cut adds Position-owned Morpho administration, collateral deployment, borrowing and repayment, synchronization, settlement, recovery, and views. It uses the configured external Morpho deployment; per-Position account contracts are created when first used. Market and oracle configuration are separate from installing the selectors.

## Source of exact phase composition

`script/libraries/StaticsProtocolPlan.sol` assigns selectors to phases. The accepted `docs/adr/staged-phase-one-launch.md` describes deployment and upgrade boundaries. Use deployed loupe and ERC-165 checks for a live Diamond. Avoid memorizing facet or selector totals from prose documents: some master documents retain older counts.
