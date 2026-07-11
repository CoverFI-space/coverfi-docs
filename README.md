# CoverFi documentation

This folder contains the public documentation site for CoverFi.

CoverFi is an open-source Stellar stablecoin protection dashboard. The project has three main surfaces:

- `mian-page`: the public landing website at `coverfi.space`
- `logic-pages`: the authenticated app at `app.coverfi.space`
- `server`: the Express API for Firebase user data, live prices, and CoverFi AI

## Local preview

Install the Mintlify CLI:

```bash
npm i -g mint
```

Run the docs site from this folder:

```bash
mint dev
```

The preview runs at `http://localhost:3000`.

## Documentation map

- `index.mdx`: product overview
- `landing-site.mdx`: marketing website behavior
- `app.mdx`: authenticated dashboard
- `ai.mdx`: CoverFi AI behavior
- `security-trust.mdx`: threat model and operational trust policy
- `economics.mdx`: fees, reserves, capacity, and payout model
- `backend-operations.mdx`: API hardening, indexes, and deployment checks
- `legal.mdx`: terms, privacy, and disclaimers
- `smart-contracts/`: Soroban contract model and deployment docs
