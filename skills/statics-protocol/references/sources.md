# Source hierarchy and freshness

This skill was synchronized against [`EqualFiLabs/statics` master](https://github.com/EqualFiLabs/statics/tree/master) at `06b2ba6c33d48b94fb8ffda53300e94b163ff7be`. Paths below are relative to that repository. Recheck master before making current-source claims. That commit is an implementation snapshot, not a production deployment claim.

## Production availability

Prefer, in order:

1. current production deployment manifests and matching onchain state;
2. runtime code hashes, immutable bindings, deployed loupe routes, and ERC-165 interfaces matched to the manifest's source commit;
3. verified deployed source and current contract reads for mutable values;
4. production-facing integration documentation.

The Genesis production record is `deployments/robinhood-mainnet-genesis.json`. Phase 1 production addresses and provenance belong in [phase-one-deployment.md](phase-one-deployment.md), filled from its reviewed deployment manifest after release. `deployments/robinhood-chain-4663.json` records target-chain dependencies, not a deployed Statics Diamond. `deployment.md` and `deployments/robinhood-testnet-46630-statics.json` describe Robinhood testnet integration evidence. A repository phase, fork test, or testnet manifest does not establish mainnet contract state.

For a live-now question, check the latest production deployment record and chain state when tools permit. If Phase 1 address fields remain unfilled, do not guess them or prepare a transaction; use the production manifest directly if one is available. If neither can be checked, describe the skill's release model and say current contract availability is unverified. Current mutable fees, quotes, ownership, rewards, pause state, and installed selectors require onchain reads.

## Standalone Genesis mechanics

Use the production manifest and these deployed-interface sources:

```text
src/interfaces/IStaticsGenesis.sol
src/interfaces/IStaticsGenesisVault.sol
src/interfaces/IGenesisActivationRegistry.sol
src/interfaces/IGenesisLaunchDistributor.sol
src/interfaces/IStaticsFeeReceiver.sol
src/genesis/StaticsGenesisVault.sol
src/genesis/GenesisActivationRegistry.sol
src/genesis/GenesisLaunchDistributor.sol
src/tokens/StaticsGenesis.sol
```

For a claim about the already deployed Genesis runtime, match current onchain code to the manifest's launch source commit before relying on master changes.

## Diamond-phase implementation

Use current compiled interfaces in `src/interfaces/` and `src/dollar/interfaces/`, the selector assignments in `script/libraries/StaticsProtocolPlan.sol`, and the accepted `docs/adr/staged-phase-one-launch.md`. `docs/integration.md`, `docs/architecture.md`, and `Statics-Design.md` explain behavior but can lag source. For client calls, check the exact installed interface and selector set of the target Diamond. Phase 2 Genesis integration uses `IStaticsGenesisIntegration` at the Diamond; it is distinct from the standalone launch distributor.

Master's README and integration prose contain older selector totals. The plan and deployed routes settle exact composition; avoid copying prose totals into user guidance.
