# Repository Guidelines

## Project Structure

| Directory              | Purpose                                                |
| ---------------------- | ------------------------------------------------------ |
| `src/`                 | TypeScript -- Next.js 16 app (routes, dashboard, DB)   |
| `src/app/`             | App Router pages and API routes                        |
| `src/lib/db/`          | SQLite domain modules (never write raw SQL in routes)  |
| `src/shared/`          | Components, constants, schemas, hooks, utils           |
| `open-sse/`            | JavaScript -- streaming engine, executors, translators |
| `open-sse/mcp-server/` | MCP Server (16 tools, 3 transports)                    |
| `src/lib/a2a/`         | A2A v0.3 protocol                                      |
| `tests/`               | unit, integration, e2e (Playwright), security, load    |
| `bin/`                 | CLI entry points                                       |
| `scripts/`             | Build helpers, postinstall, system-info                |

Path aliases: `@/*` = `./src/*`, `@omniroute/open-sse` = `./open-sse`.

## Build, Test, and Development

```bash
npm run dev          # Dev server with hot reload
npm run build        # Production build (must pass before commit)
npm run lint         # ESLint + TypeScript check
npm test             # Unit tests (Node.js test runner, 707+ tests)
npm run test:vitest  # Vitest MCP/autoCombo tests
npm run test:e2e     # Playwright E2E tests
npm run check        # lint + test combined
```

Single test: `node --test tests/unit/someFile.test.mjs`

## Coding Style

- **camelCase** for all variables, functions, and properties.
- **Prettier** + **ESLint** enforced via lint-staged on commit.
- Security rules: `no-eval`, `no-implied-eval`, `no-new-func` (error level).
- Input validation with **Zod** schemas (`src/shared/schemas/`).
- `src/lib/localDb.ts` is a re-export layer only -- add logic to `src/lib/db/*.ts`.

## Testing

- Framework: Node.js built-in test runner (`.test.mjs`), Vitest for MCP, Playwright for E2E.
- Mocks allowed **only** in unit tests, never in production code.
- Pre-commit hook runs the full unit suite (~100s). All tests must pass.

## Commit Guidelines

- **Conventional Commits**: `type(scope): description`
- Scopes: `db`, `sse`, `oauth`, `dashboard`, `api`, `cli`, `docker`, `ci`, `build`
- Build must succeed before committing.

## Adding a Provider

1. Register in `src/shared/constants/providers.ts`
2. Create executor in `open-sse/executors/`
3. Add translator in `open-sse/translator/` (if non-OpenAI format)
4. Add OAuth config in `src/lib/oauth/constants/oauth.ts` (if OAuth-based)

## Architecture Notes

- DB operations go through `src/lib/db/` modules -- never raw SQL in routes.
- MCP and A2A pages are tabs inside `/dashboard/endpoint`, not standalone routes.
- Dashboard uses the `(dashboard)` route group layout.
- Encryption at rest: AES-256-GCM. Auth: JWT cookies (dashboard), HMAC Bearer keys (API).
