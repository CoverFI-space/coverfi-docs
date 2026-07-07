# Documentation project instructions

## About this project

- This is the Mintlify documentation site for CoverFi.
- CoverFi is an open-source Stellar stablecoin protection dashboard.
- Use "CoverFi" for the product and website name.
- Use "CoverFi AI" for the assistant.
- Pages are MDX files with YAML frontmatter.
- Navigation and site settings live in `docs.json`.

## Product terminology

- Use "landing site" for `mian-page`.
- Use "app" or "authenticated app" for `logic-pages`.
- Use "backend" or "API" for `server`.
- Use "protection position" for a user's stablecoin protection record.
- Use "payment draft" for AI-generated payment instructions. Do not call it an executed payment or receipt.
- Use "Research mode" for CoverFi AI responses using the research model path.

## Style preferences

- Use active voice and second person.
- Keep sentences concise.
- Use sentence case for headings.
- Bold UI labels, for example **CoverFi AI**.
- Code-format file names, commands, paths, routes, environment variables, and model names.
- Keep security boundaries clear: CoverFi AI can draft guidance, but wallet signing is always user-controlled.

## Content boundaries

- Do not document unfinished features as production-ready.
- Do not invent contract addresses, wallet addresses, repository URLs, or deployment hosts.
- If a setting is environment-specific, describe the variable and give safe placeholder values.
