# Phase 1 Robinhood Chain deployment record

This is the address record for the live Phase 1 guidance. Complete the fields from the reviewed production deployment manifest after deployment. An unfilled field is never a contract address or permission to submit a transaction. For an action before this record is filled, use a verified production manifest directly and perform the same chain and runtime checks.

| Field | Production value |
| --- | --- |
| Chain ID | `4663` |
| Deployment manifest URL and source commit | `TO_FILL_AFTER_DEPLOYMENT` |
| Deployment block and transaction record | `TO_FILL_AFTER_DEPLOYMENT` |
| `StaticsDiamond` | `TO_FILL_AFTER_DEPLOYMENT` |
| `StaticsTimelock` | `TO_FILL_AFTER_DEPLOYMENT` |
| Public `StaticsSwapFeeHook` | `TO_FILL_AFTER_DEPLOYMENT` |
| `StaticsLiquidityManager` | `TO_FILL_AFTER_DEPLOYMENT` |
| `StaticsPermissionedSwapFeeHook` | `TO_FILL_AFTER_DEPLOYMENT` |
| Trusted `StaticsPermissionedRouter` | `TO_FILL_AFTER_DEPLOYMENT` |
| `StaticsPermissionedPositionManager` | `TO_FILL_AFTER_DEPLOYMENT` |
| `PermissionedPositionClaims` | `TO_FILL_AFTER_DEPLOYMENT` |
| `DefaultVenueControllerFactory` | `TO_FILL_AFTER_DEPLOYMENT` |

The existing STATICS token, WETH, and Genesis addresses come from `deployments/robinhood-mainnet-genesis.json`. The chain's PoolManager, PositionManager, and Permit2 addresses come from `deployments/robinhood-chain-4663.json`; that dependency file does not prove that Statics Phase 1 is installed. Read the deployed Phase 1 release manifest for the exact runtime hashes and configuration rather than copying a testnet artifact.

Before using an address, verify chain ID, nonzero code and manifest runtime hash, Diamond loupe routes and relevant ERC-165 interfaces, timelock ownership, hook and manager immutable bindings, trusted permissioned periphery, current creation fees, pause/restriction state, and the target PoolId or PositionNFT. Use the manifest's source commit for ABIs. If the manifest and deployed state disagree, stop the affected action and report the mismatch.
