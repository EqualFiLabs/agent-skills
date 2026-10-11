# Statics phased rollout

This guide presents Statics Genesis and Phase 1 as the user experience. Phase 1 production addresses are supplied in [phase-one-deployment.md](phase-one-deployment.md) after release. Later protocol phases are implemented in Statics master but require separate production activation. Check [sources.md](sources.md) for the evidence needed to answer a current deployment question.

## Statics Genesis

The production Genesis manifest records the STATICS token, 5,555 Operators, their fully backed vault and native reserve, activation tiers, launch reward distributor, permanent STATICS/WETH Doppler market, and Genesis Secured Credit. These contracts and market are separate from the Phase 1 protocol contract. See [live-genesis.md](live-genesis.md) for addresses and mechanics; verify mutable state onchain.

## Live Phase 1: markets, PositionNFTs, and staking

Phase 1 uses one protocol address for public and permissioned Uniswap v4 market paths, PositionNFT accounts, global STATICS staking and selected-asset rewards, managed public LP positions, range gauges, weekly reserve-backed protocol incentives, direct funded reward slots, typed batch claims, market views, and guardian stops. Public pools support compatible ERC-20 pairs and native ETH/ERC-20 pairs. Permissioned venues have their own hook, controller, trusted periphery, approved non-transferable LP positions, and fee policy. See [phase-one.md](phase-one.md) for actions and boundaries.

The Robinhood mainnet launch configuration sets public pool creation to **0.01 ETH**. Anyone may create a public pool with the exact quoted native payment; relayed creation also requires the creator's authorization, while a direct creator needs no signature. Creation does not seed liquidity automatically. Read the deployed fee and `quotePool(params)` before a transaction. The launch configuration also sets **0.001 ETH** per PositionNFT and **0.025 ETH** for optional managed-POL activation; read deployed values before payment.

The deployment signer configures Phase 1, then transfers protocol ownership to the governance Safe. Launch operators create and verify an initial public pool before the Safe transfers ownership to the timelock. Permissioned pool creation remains owner-only, so the Safe may create an initial venue and later requests go through the timelock. Fee-paid public pool creation requires no governance transaction.

Phase 1 does not install basket, self-backed credit, flash-loan, protocol-side Genesis integration, Statics Dollar, Morpho, or borrow-to-liquidity selectors. Its gauge allocation uses raw staked STATICS, not Operator activation multipliers.

## Phase 2: baskets, credit, flash composition, and Genesis integration

The next timelocked upgrade adds fixed-composition BasketTokens, basket creation and redemption, basket collateral and rewards, proportional self-backed credit, canonical basket pools and liquidity, borrow-to-liquidity, basket-vector and single-asset flash loans, and protocol-side Genesis Operator linkage and rewards. Existing Genesis contracts are not redeployed by this upgrade. A separate governance handoff binds the new integration surface.

Each BasketToken represents a fixed constituent bundle; its composition does not automatically rebalance. A basket's creation and liquidity configuration remain distinct from general-pool creation.

## Phase 3: Statics Dollar

The next upgrade adds Dollar periphery and a separate Dollar Core custody and solvency boundary, plus the senior `USDstx` token and ERC-1155 Risk Shares. The implemented design includes volatile and pegged collateral profiles, issuance, recombination, health controls, insurance, transition, and recovery. Dollar Core and the main protocol have different roles even though they share timelock governance.

## Phase 4: Morpho

The final upgrade adds Position-owned Morpho administration, collateral deployment, borrowing and repayment, synchronization, settlement, recovery, and views. It uses the configured external Morpho deployment; per-Position account contracts are created when first used. Market and oracle configuration are separate from installing the selectors.

## Source of exact phase composition

`script/libraries/StaticsProtocolPlan.sol` assigns selectors to phases. The accepted `docs/adr/staged-phase-one-launch.md` describes deployment and upgrade boundaries. Use deployed selector and ERC-165 checks for the protocol. Verify current facet and selector totals against the plan and deployed routes rather than memorizing prose counts.
