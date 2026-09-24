# Source Hierarchy

Use this hierarchy to avoid confusing repository scope with production scope.

## For what is live

Prefer, in order:

1. production deployment manifests and deployed onchain state;
2. runtime code hashes and the source commit recorded by the production manifest;
3. live contract interfaces and verified source matching the deployed runtime;
4. production-facing integration documentation.

Primary Genesis deployment record:

```text
EqualFiLabs/statics/deployments/robinhood-mainnet-genesis.json
```

For mutable values, deployed contract state overrides launch defaults and documentation.

## For exact Genesis mechanics

Use:

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

## For planned phases

At initial publication, the phase map is based on EqualFiLabs/statics PR #96.

Open PRs are planning evidence, not production deployment evidence.

## Design references

Statics-Design.md, ADRs, README sections, test suites, SDK code, and testnet manifests explain architecture and implemented behavior. They do not prove a feature is production live.

## Testnet

Robinhood Chain testnet artifacts are integration evidence only.

## Freshness rule

If the user asks whether something is live now, current, deployed, available today, or changed recently, verify production deployment evidence when tool access permits.

If current verification is unavailable, state the Skill's last-known status and distinguish it from verified current status.
