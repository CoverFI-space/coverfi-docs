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
- `quickstart.mdx`: local setup
- `architecture.mdx`: project structure and data flow
- `landing-site.mdx`: marketing website behavior
- `app.mdx`: authenticated dashboard
- `ai.mdx`: CoverFi AI behavior
- `api-reference.mdx`: backend routes
- `smart-contracts.mdx`: Soroban contract model
- `configuration.mdx`: environment variables
- `deployment.mdx`: production setup
- `updates.mdx`: recent product updates
