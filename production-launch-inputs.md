# CoverFi Production Launch Inputs

This checklist explains what information is needed to finish the external production gates.

Do not paste private keys, Redis tokens, oracle API secrets, AI provider keys, or deployment credentials into chat. Put secrets in the relevant local `.env` file or deployment secret manager, then tell the agent which variable names are set.

## 1. Admin And Multisig

Current local/testnet scripts use the Stellar CLI identity named `alice`:

```txt
--source alice
```

For testnet, `alice` can remain the deployer/admin identity.

For production, provide:

- Network: `testnet` or `mainnet`.
- Admin account public key or multisig account public key.
- Deployer source identity name, for example `alice`.
- Oracle updater public key.
- Pause operator public key.
- Treasury public key if protocol revenue is enabled later.
- Required signer threshold, for example `2-of-3` for routine admin and `3-of-5` for upgrades.

Important: most current contracts set admin during `initialize`. They do not all expose a post-deploy `transfer_admin` function. To move admin after deployment, either redeploy with the final admin address or add and audit explicit admin-transfer functions.

## 2. Redis Or Upstash Rate Limits

The backend currently uses process-local rate limits. For production, create one of these:

### Option A: Upstash Redis

1. Create an Upstash Redis database.
2. Copy the REST URL and REST token.
3. Add these to `server/.env` or deployment secrets:

```txt
UPSTASH_REDIS_REST_URL=...
UPSTASH_REDIS_REST_TOKEN=...
RATE_LIMIT_BACKEND=upstash
```

### Option B: Redis URL

1. Create Redis through Redis Cloud, Azure Cache, Railway, Render, Fly, or another provider.
2. Copy the TLS Redis URL.
3. Add this to `server/.env` or deployment secrets:

```txt
REDIS_URL=rediss://...
RATE_LIMIT_BACKEND=redis
```

After setting one option, tell the agent which option you chose.

## 3. Oracle Provider Inputs

For each protected asset, provide:

- Asset symbol, for example `XLM`, `USDC`, `EURC`.
- Stellar asset contract ID.
- Price feed sources, at least three for production if using custom aggregation.
- Source API URLs or provider feed IDs.
- Whether each source needs an API key.
- Price scale, for example `100000000` means 1.00000000.
- Max oracle age in seconds.
- Maximum allowed source deviation in basis points.
- Update cadence, for example every 60 seconds.
- Oracle signer identity/public key.

Minimum production policy:

```txt
sources: at least 3
aggregation: median
max_deviation_bps: 50 to 100 for stable assets
max_oracle_age_seconds: 300 to 900 for mainnet stablecoin protection
emergency_action: pause protection creation and settlement automation
```

If using an established Stellar-compatible oracle provider, provide the provider name, feed IDs, network, and integration docs.

## 4. Formal Audit Requirements

Before a public mainnet launch, prepare:

- Frozen commit hash and deployed contract IDs.
- Contract source tree and WASM hashes.
- Initialization parameters and admin addresses.
- Threat model and known-risk list.
- Economic model: reserve caps, max payout, fee routing, provider NAV, locked liabilities, reserved claims, withdrawal queue, and stress scenarios.
- Test suite results.
- Deployment scripts.
- Upgrade and pause runbooks.
- Scope list: protection engine, protected balance vault, reserve vault, oracle adapter, username registry, receipt registry.

Ask auditors to focus on:

- Authorization and initializer capture.
- Cross-contract calls and token movement.
- Reserve accounting and locked/reserved capacity.
- Claim lifecycle and double-claim prevention.
- Oracle staleness, manipulation, and fallback behavior.
- Admin/pause/upgrade abuse paths.
- Receipt uniqueness and privacy assumptions.
- Economic insolvency or correlated-claim scenarios.

## 5. Legal Review Requirements

Give counsel:

- Product description and screenshots.
- Terms and privacy pages.
- Jurisdictions where users will be allowed.
- Whether users pay fees/premiums.
- Whether CoverFi markets protection, cover, payout, claim, reserve, or insurance-like wording.
- Whether any entity controls reserve funds, oracle updates, admin keys, or claim operations.
- Data map: wallet-local encrypted app/profile/receipt/AI data, public chain data, wallet addresses in contract events, HMAC log/analytics identifiers, aggregate analytics, and AI provider data.
- Retention, export, and deletion policy.
- Incident response and customer support process.

Ask counsel to decide:

- Whether this is insurance, warranty, derivative, financial product, or software service in target jurisdictions.
- Required disclaimers and prohibited marketing language.
- KYC/AML/sanctions requirements.
- Consumer protection and refund obligations.
- Privacy rights and data-processing agreements.
