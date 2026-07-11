---
title: "Security & trust model"
description: "Threat model, operational policies, and audit readiness for CoverFi"
---

CoverFi is beta software. This page defines the security posture reviewers should evaluate before mainnet use.

## Trust boundaries

| Boundary | Current control | Production requirement |
| --- | --- | --- |
| Wallet actions | Freighter prompts users to sign transactions | Add signed session challenges for backend write access |
| Backend state | Wallet header ownership checks, schema validation, rate limits | Replace header convention with wallet-signed auth tokens |
| Price data | Oracle adapter stores admin-updated scaled prices and the engine rejects stale prices | Use multi-source oracle feeds and monitoring |
| Reserve payouts | Epoch allocation, reserved claimables, 20% floor, 50% surplus drip | Add keeper/operator automation and audit the reserve policy |
| Receipts | Structured receipt records are stored in Firebase | Add retention controls, export/delete workflows, and anomaly alerts |
| AI | Support and draft generation only | Keep AI non-custodial; never allow autonomous signing |

## Threat model

Primary threats:

- Cross-wallet writes to account state, payments, receipts, or AI history.
- Username squatting, enumeration, and payment-history scraping.
- AI prompt abuse, long prompt cost attacks, and sensitive-data disclosure.
- Oracle stale price or manipulated price updates.
- Admin key compromise.
- Reserve underfunding or incorrect capacity accounting.
- Receipt/history write abuse and malicious metadata.

Current mitigations:

- Route-level request validation.
- Route-specific rate limiting for auth, payments, receipts, and AI.
- Wallet ownership headers on private backend routes.
- Full transaction-hash validation for saved payment receipts.
- Firebase-backed structured receipt storage.
- Contract-level oracle staleness checks.
- Restricted payout accounting in the reserve vault.
- Clear product disclaimer: CoverFi protection is not insurance and payouts are not guaranteed.

## Oracle staleness policy

The current engine stores `max_oracle_age_seconds` and rejects trigger checks when the oracle timestamp is missing or stale. The current testnet deployment uses `3600` seconds.

Before production, oracle operations must also include:

- Price timestamp stored beside each asset price.
- Maximum freshness window per asset, for example 5 minutes for stablecoin triggers on mainnet.
- Off-chain monitoring that alerts on delayed price pushes, abnormal moves, or missing feeds.

## Reserve payout policy

The reserve vault separates claim validation, epoch allocation, and user withdrawal:

1. The engine validates a claim and records approved claim value.
2. The reserve vault closes epochs with a solvency floor and surplus drip.
3. Users withdraw only their reserved allocation.

Current testnet policy:

- `floor_bps = 2000`
- `drain_bps = 5000`

This is designed to reduce sequential-withdrawal first-mover advantage. It is still beta logic and must be audited before mainnet.

## Admin key policy

Production admin keys should follow:

- Multisig control for contract initialization, upgrades, oracle permissions, and pause authority.
- Separate keys for deployment, oracle updates, operations, and emergency response.
- Hardware-backed custody where available.
- Rotation runbook and documented recovery contacts.
- No admin private keys in frontend, backend source, logs, CI output, or docs.

## Pause and upgrade policy

CoverFi contracts and backend services should only be upgraded through a documented release process:

1. Publish a change summary.
2. Run contract tests and backend route tests.
3. Verify deployment addresses and Firestore indexes.
4. Announce user-facing risk if behavior changes.
5. Use emergency pause only for active exploitation, oracle failure, reserve accounting failure, or critical data exposure.

## Audit readiness checklist

- Contract tests cover happy paths, unauthorized calls, invalid amounts, double claims, expiry, and reserve capacity.
- Backend tests cover private routes, schema validation, rate limits, and AI abuse cases.
- Oracle staleness behavior is implemented and tested.
- Restricted payout reserve accounting is implemented and tested.
- Admin/pause/upgrade permissions are reviewed independently.
- Deployment addresses, initialization order, and reserve funding are documented.
- Legal copy clearly states protection is not insurance or guaranteed payout.
