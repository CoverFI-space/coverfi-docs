---
title: "Security & trust model"
description: "Threat model, operational policies, and audit readiness for CoverFi"
---

CoverFi is beta software. This page defines the security posture reviewers should evaluate before mainnet use.

## Trust boundaries

| Boundary | Current control | Production requirement |
| --- | --- | --- |
| Wallet actions | Freighter prompts users to sign transactions | Keep clear signing and add more transaction-state recovery UX |
| Backend state | Wallet-signed challenge/session auth, schema validation, rate limits | Add persistent revocation, distributed rate limits, and auth monitoring |
| Price data | Oracle adapter stores timestamped observations; Testnet can use controlled publisher or source refresh paths; engines reject stale prices | Use documented multi-source oracle feeds, monitoring, and emergency controls |
| Reserve payouts | Position-specific payout locks, reserved claims, provider NAV, withdrawal queue, restricted balances | Audit the reserve policy and run settlement/withdrawal drills |
| Receipts | Canonical receipt hashes, duplicate payment-hash rejection, wallet-local encrypted app history | Add retention policy, UI controls, and anomaly alerts |
| AI | Support and draft generation only | Keep AI non-custodial; never allow autonomous signing |

## Threat model

Primary threats:

- Cross-wallet writes to browser-local account state, payments, receipts, or AI history.
- Username squatting, enumeration, and payment-history scraping.
- Payment misdirection through stale or incorrect username resolution.
- AI prompt abuse, long prompt cost attacks, and sensitive-data disclosure.
- Oracle stale price or manipulated price updates.
- Admin key compromise.
- Reserve underfunding or incorrect capacity accounting.
- Receipt/history write abuse and malicious metadata.

Current mitigations:

- Route-level request validation.
- Route-specific rate limiting for auth, payments, receipts, and AI.
- Signed wallet sessions on private backend routes.
- Full transaction-hash validation for saved payment receipts.
- Duplicate payment-hash rejection in the receipt registry.
- Browser-local encrypted export/delete controls.
- Wallet-local structured receipt history after signed payments.
- Wallet-signed username payments that resolve recipient addresses through the Soroban username registry before submitting a Stellar payment.
- Contract-level oracle staleness and expiry-settlement checks.
- Position-specific payout reservation in the reserve vault.
- Clear product disclaimer: CoverFi protection is not insurance and payouts are not guaranteed.

## Oracle staleness policy

The current engine stores `max_oracle_age_seconds`, captures a fresh entry observation when a position opens, and settles positions using the latest valid observation at or before expiry. Missing, stale, future, or invalid observations block unsafe settlement paths.

The current oracle architecture separates source and adapter responsibilities. `quorum_oracle_source` can accept publisher submissions and expose a source price. `oracle_adapter` normalizes source prices, stores observations per asset, rejects excessive deviation, supports fallback publisher submissions, and exposes fresh/latest/historical observation getters.

Before production, oracle operations must also include:

- Price timestamp stored beside each asset price.
- Maximum freshness window per asset, for example 5 minutes for production settlement-sensitive markets.
- Off-chain monitoring that alerts on delayed price pushes, abnormal moves, or missing feeds.
- Operator-gated mainnet publishing routes.
- Documented publisher custody, rotation, and emergency pause procedures.

## Reserve payout policy

The reserve vault separates position capacity, settlement reservation, owner payout claims, principal withdrawals, and provider withdrawals:

1. The engine locks maximum payout capacity when a position opens.
2. Expiry settlement releases unused capacity or reserves the full calculated payout.
3. The owner claims any reserved payout through the engine.
4. The owner withdraws protected principal through a separate engine call.
5. Reserve providers withdraw through shares, NAV, cooldown, and available-liquidity checks.

Current V2 testnet routing:

- `underwriting_bps = 7800`
- `protocol_bps = 1000`
- `safety_bps = 700`
- `automation_bps = 500`

This is designed to keep locked liabilities, reserved claims, unearned premiums, safety funds, and automation funds out of provider withdrawals. It is still beta logic and must be audited before mainnet.

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
3. Verify deployment addresses, status endpoints, and frontend contract IDs.
4. Announce user-facing risk if behavior changes.
5. Use emergency pause only for active exploitation, oracle failure, reserve accounting failure, or critical data exposure.

## Audit readiness checklist

- Contract tests cover happy paths, unauthorized calls, invalid amounts, double claims, expiry, and reserve capacity.
- Backend tests cover private routes, schema validation, rate limits, and AI abuse cases.
- Oracle staleness behavior is implemented and tested.
- Position-specific payout reservation and reserve-provider withdrawal accounting are implemented and tested.
- Admin/pause/upgrade permissions are reviewed independently.
- Deployment addresses, initialization order, and reserve funding are documented.
- Legal copy clearly states protection is not insurance or guaranteed payout.
