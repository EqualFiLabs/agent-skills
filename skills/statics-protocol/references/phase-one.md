# Phase 1 source guide

Phase 1 is the Statics Diamond user surface on Robinhood Chain. Resolve its contracts from [phase-one-deployment.md](phase-one-deployment.md) or the reviewed production manifest; the address fields in this skill are completed after deployment. Before planning a transaction, confirm chain ID 4663, the target Diamond, installed phase and relevant ERC-165 interfaces and selectors, hook and liquidity-manager bindings, current configuration, and quotes from the actual chain. Use the deployed source commit's compiled interfaces and `docs/integration.md` for exact calldata.

## Action preflight

For each user-authorized Phase 1 action, identify the acting account and intended receiver, read the position or PoolId and current authorization, calculate token and native spend, and simulate with the actual caller. Confirm the current pool status and pause/restriction state. Use exact or bounded approvals and user-specified minimum outputs. After submission, verify the receipt and re-read the affected position, reward book, or pool; a successful broadcast alone is not completion.

- **Create a public general pool:** Read `poolCreationFee()` and `quotePool(params)`. A zero fee reserves creation to the Diamond owner; a nonzero fee requires its exact native payment and the applicable creator authorization or direct-creator path. Verify the resulting PoolId and PoolKey, then provision liquidity separately if requested.
- **Create or stake a PositionNFT:** Read the Position creation fee, configured staking token, current reward-asset limit, token balance and allowance. Use `createPosition` or `createAndStake`, then `stake`, `optInRewardAssets`, or `optOutRewardAssets` as needed. Show pending eligibility and the independent allocation cooldown before estimating rewards or unstaking.
- **Manage a public LP position:** Verify the PoolKey, LP NFT ownership or approval, PositionNFT authority, manager binding, native funding amount, deadlines, and output minimums. `provideLiquidity` and `attachLiquidity` place the underlying v4 NFT into managed custody; `collectNativeFees`, `decreaseLiquidity`, `rebalanceLiquidity`, and `exitLiquidity` have separate effects on principal, fees, and rewards. External v4 LP NFTs remain outside Statics until attached.
- **Use a permissioned venue:** Read its PoolId-local controller and allowed trader/LP state. Route exact-input swaps through the trusted permissioned periphery, and check its approved non-transferable LP path. Public pool permissions and POL assumptions do not apply.
- **Claim rewards:** Discover actual global, LP, and allocator entitlements, read current selectors and claim limits, choose receivers and minimums, and simulate the complete claim or batch. A separate batch is needed for standalone Operator rewards.

## Market paths

- **Public general pools:** Compatible ERC-20 pairs or native ETH/ERC-20 pairs use the public Statics hook. Creator-selected static native v4 LP fees and governed bilateral hook fees are separate charges. Creator, treasury, global staker, and optional PoolId-bound POL accounting use the hook-fee path. A pool can be created without an initial liquidity seed. Read `poolCreationFee()` and `quotePool(params)`; a zero creation fee means only the Diamond owner can create, while a nonzero fee enables the permissionless signed-creator path. General POL starts disabled unless the creator pays its governed activation fee.
- **Permissioned venues:** A separate hook and trusted exact-input periphery enforce controller policy and approved, non-transferable LP positions. Their PoolId-local output fee has no POL allocation. Do not assume that a public pool's owner, reward, routing, or LP rules apply to a permissioned venue.
- **Standalone STATICS/WETH market:** The Doppler Genesis market remains separate from these Phase 1 pools. Use its recorded PoolKey and normal v4 routing; do not substitute a Diamond pool address or hook.

Public and permissioned pool creator authority can transfer through a propose/accept flow. A creator may set a separate revenue recipient. Read the current creator and effective recipient before preparing fee claims or permissioned configuration. Reward restrictions, venue-controller replacement, pool stops, and decommissioning have distinct effects on new activity and accrued claims; inspect the installed pool interface and current state instead of inferring them from creation-time settings.

Public-pool native ETH stays native for swap settlement, LP and POL principal, refunds, and native LP fees. Reward accounting wraps realized ETH revenue into WETH. A WETH reward opt-in covers both WETH and converted ETH hook revenue; this does not turn native principal into WETH. See `docs/adr/native-eth-phase-one.md`.

## PositionNFT, staking, and liquidity

`StaticsDiamond` is the PositionNFT ERC-721 and ordinary user action address. A PositionNFT may own a staking leg and managed public LP positions; an ordinary external Uniswap v4 PositionManager NFT remains outside it unless its owner opts into managed custody. Read position state and underlying leg economics before transfer. ERC-2981 royalty data signals marketplace terms but does not tax an ordinary ERC-721 transfer.

Global reward selection and gauge allocation are separate. A new stake or reward-asset selection waits for the next hourly boundary at least 24 hours later before earning global selected-asset rewards. Read `maxRewardAssetsPerPosition()` and `hardMaxRewardAssetsPerPosition()` rather than hard-coding the selection limit. The reward delay is not an unstake lock. Positive staking ingress also starts or extends the separate governed gauge-allocation cooldown; existing allocations may be reduced or removed during it, and unallocated stake remains withdrawable. Gauge routing weights raw staked STATICS and does not use Operator tiers.

Each public range gauge has protocol-funded STATICS slot 0 and four independently funded direct reward slots. Direct-slot funders can protect their intended LP/allocator split; a creator may assign part of future direct funding to valid PoolId allocators. Reserve release is scheduled in anchored seven-day periods and routed continuously by allocation weight. Claims and accounting may need bounded permissionless checkpoints. No reward is automatic or guaranteed: fees and incentives require actual routed activity or funding.

## Claims and statements

Phase 1 provides individual global, LP, and allocator claims and typed atomic batch claims. The batch interface bounds inputs to 16 groups and 64 requested assets or slots, but those bounds do not guarantee gas fit. It performs no discovery or automatic catch-up. Simulate the complete batch with the actual caller, receiver, and minimums. The separate aggregated batch interface combines exact token payouts and rejects incompatible transfer behavior; use ordinary individual or batch claims for taxed tokens. These interfaces do not include standalone Genesis or later basket claims.

PositionNFT statement events attribute actual wallet debits and credits for managed liquidity, allocations, and reward claims. Internal rebalance settlement is not a wallet payout; an event or submitted transaction alone is not proof of final onchain state. See `docs/integration/position-statements.md` and reconcile receipts with current state.

The market tape exposes canonical per-PoolId counters and sequence numbers. Historical observation writes are bounded and best effort, so reconcile gaps and failures before using a historical series. Permissioned external venue volume and internal fee-normalization activity are separate counters; the current native LP fee rate is not a cumulative LP-fee amount.

## Source routing

- [`docs/adr/staged-phase-one-launch.md`](https://github.com/EqualFiLabs/statics/blob/master/docs/adr/staged-phase-one-launch.md): phase boundary, installation, governance, and current selector plan.
- [`docs/integration.md`](https://github.com/EqualFiLabs/statics/blob/master/docs/integration.md): canonical ABIs, market creation, staking, liquidity, rewards, and batch claims.
- [`docs/adr/native-eth-phase-one.md`](https://github.com/EqualFiLabs/statics/blob/master/docs/adr/native-eth-phase-one.md): native currency settlement and WETH reward boundary.
- [`docs/adr/weekly-protocol-gauge-incentives.md`](https://github.com/EqualFiLabs/statics/blob/master/docs/adr/weekly-protocol-gauge-incentives.md): slot 0 reserve, allocation cooldown, and continuous routing.
- [`docs/adr/statics-market-tape.md`](https://github.com/EqualFiLabs/statics/blob/master/docs/adr/statics-market-tape.md): canonical market counters and observation semantics.
- [`docs/integration/position-statements.md`](https://github.com/EqualFiLabs/statics/blob/master/docs/integration/position-statements.md): event meanings and statement attribution.
