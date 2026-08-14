# Production Security Runbook

This runbook is a launch gate for mainnet-scale CoverFi deployments. Testnet deployments may use narrower controls for development, but production must not.

Required external launch inputs are tracked in [`production-launch-inputs.md`](production-launch-inputs.md).

## Oracle Policy

CoverFi must not rely on a single manually updated oracle signer in production.

Required production policy:

- Use at least three independent price sources or one established Stellar-compatible oracle provider with independent source aggregation.
- Require median or quorum-based price acceptance before publishing a price used by `protection_engine`.
- Enforce `max_oracle_age_seconds` on-chain and alert before prices approach the stale threshold.
- Pause new position creation and settlement automation if price sources diverge beyond the configured deviation threshold. Owner exits for already-settled positions should remain available wherever contract state permits.
- Publish oracle operator addresses, source list, max age, deviation threshold, and emergency contacts in deployment notes.

Emergency oracle failure flow:

1. Pause the protection engine.
2. Stop oracle updates from the suspected source.
3. Compare accepted price history against independent market data.
4. Publish an incident note with affected assets, last accepted price, and next action.
5. Resume only after quorum sources are healthy and signer state has been reviewed.

## Admin And Multisig Policy

Production admin authority must use Stellar multisig or an audited admin controller contract.

The local Stellar CLI identity `alice` may be used as a testnet deployer/admin identity. It is not a production multisig substitute.

Required role split:

- Deployer: deploys code, then transfers admin authority.
- Protocol admin: updates fees, max payout, oracle age, and vault policy.
- Pause authority: can pause quickly during incidents.
- Oracle publisher: publishes prices only.
- Treasury/reserve operator: funds reserves and monitors provider withdrawals, reserved claims, safety balances, and automation balances.

Required thresholds:

- Protocol admin actions: at least 2-of-3 signers.
- Upgrade or admin transfer actions: at least 3-of-5 signers.
- Emergency pause: at least 1 dedicated pause signer plus post-incident review.
- Unpause: at least 2-of-3 protocol admin signers.

## Backend Wallet Sessions

Production must set `AUTH_SESSION_SECRET` to a high-entropy secret in the deployment environment. The server refuses to start in production without it. Local development can still use a process-local fallback, which invalidates sessions on restart.

## Pause Runbook

Use pause when oracle health, reserve accounting, contract behavior, or frontend transaction construction is suspect.

1. Pause `protection_engine`.
2. Pause or disable dependent frontend write flows.
3. Snapshot affected contract IDs, ledger range, config, and pending transactions.
4. Announce impact and expected next update.
5. Root-cause the issue before unpause.
6. Unpause only after multisig approval and a successful testnet rehearsal if contract behavior changed.

## Upgrade Runbook

1. Build reproducible WASM artifacts from a tagged commit.
2. Run contract tests for the full workspace.
3. Deploy to testnet and run initialization plus end-to-end transaction rehearsal.
4. Compare contract IDs, init args, admin roles, oracle config, and reserve balances against the deployment checklist.
5. Obtain multisig approval for mainnet deployment or upgrade.
6. Publish final contract IDs and config values.
7. Monitor health, oracle freshness, and transaction errors for at least 24 hours.
