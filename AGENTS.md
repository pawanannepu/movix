# Movix

A responsive web app that learns your movie taste and gives you a personalized feed that explains each pick.

## Stack

- **Language / Runtime**: TypeScript (strict), Node 24 LTS (pinned in `engines` and `.nvmrc`)
- **Framework**: Next.js 16 (App Router, Cache Components off for Release 1), React 19, Tailwind CSS 4
- **Decided, installed by the feature that needs them**: Postgres with pgvector on Neon, Drizzle ORM, Better Auth, Zod
- **Package manager**: pnpm 10 (pinned in `packageManager`), one package at the repo root
- **Hosting**: Vercel. Full decision: `docs/specs/0001-stack-and-architecture/index.md`

## Build approach

Skateboard: ship the thinnest usable whole first, then grow it release by release, each one shippable.

## Commands

```bash
pnpm install   # install
pnpm dev       # dev server on http://localhost:3000
pnpm build     # production build (also typechecks)
pnpm test      # not installed yet: Vitest, set up by /test
```

Lint, format, and a `typecheck` script are not installed yet. `/develop tooling` adds them (see Tooling).

## Git

- integration: on
- branch prefix: feat/
- commit: per-milestone

## Specs

Stored in `docs/specs/`. Format: `docs/specs/NNNN-title.md`. The plan lives in `docs/scope/`.

## Rules

- Functions are pure by default. Push side effects (database, network, files) to the edges and keep them explicit.
- Data is immutable (`const`, `readonly`, never mutate in place). Module level variables are constants only.
- Prefer plain functions and composition over classes. Avoid `null`; use `undefined` or union types.
- Expected failures return a typed result, not a thrown exception. Use one error result shape everywhere, set in the first feature that needs it.
- TypeScript strict, no `any`. Validate all external input and environment variables with Zod. Secrets live only in environment variables.
- All database access goes through the one Drizzle client (raw SQL only through its `sql` template), in server code only.
- Layout: `src/app`, `src/db`, `src/auth`, `src/lib`, `scripts/`. Feature code lives by feature in `src/features/<name>/` (components, actions, queries together). Shared UI goes in `src/components`.
- Next.js changes fast. Read the installed docs in `node_modules/next/dist/docs/` before writing Next.js code.

## Tooling

Chosen here, installed by `/develop tooling`:

- Lint and format: ESLint with `eslint-config-next`, plus Prettier.
- Before each commit: Husky with lint-staged runs lint, format, and typecheck on changed files.
- Tests: Vitest for unit and integration (runner set up by `/test`), plus `/check verify` for real behavior.
- CI: a basic GitHub Actions check on push and pull request (install, lint, typecheck, test, build).

## Agent skills

- [nextjs-app-router-patterns](.agents/skills/nextjs-app-router-patterns/): `wshobson/agents`, App Router, server components, Server Actions
- [vercel-react-best-practices](.agents/skills/vercel-react-best-practices/): `vercel-labs/agent-skills`, React and Next.js performance
- [next-dev-loop](.agents/skills/next-dev-loop/): `vercel/next.js`, verify runtime behavior in a running `next dev`
- [deploy-to-vercel](.agents/skills/deploy-to-vercel/): `vercel-labs/agent-skills`, deploys and previews
- [drizzle-orm-patterns](.agents/skills/drizzle-orm-patterns/): `giuseppe-trisciuoglio/developer-kit`, schema, queries, migrations
- [neon-postgres](.agents/skills/neon-postgres/): `neondatabase/agent-skills`, connections, branching, pooled vs direct
- [tailwind-design-system](.agents/skills/tailwind-design-system/): `wshobson/agents`, Tailwind v4 tokens and components
- [zod](.agents/skills/zod/): `pproenca/dot-skills`, schemas, parsing, error handling
- [vitest](.agents/skills/vitest/): `antfu/skills`, unit testing, mocking, coverage
- [setup-pre-commit](.agents/skills/setup-pre-commit/): `mattpocock/skills`, Husky and lint-staged hooks

MCP servers: Neon (recommended), Vercel (recommended)

## Context files

<!-- Nested AGENTS.md files are listed here as they are created -->

_Drafted by /audit from the repo, worth a quick human pass. Edit freely: once a line stops matching this draft, later runs treat it as curated and will flag rather than overwrite it._

<!-- BEGIN:nextjs-agent-rules -->

## This is NOT the Next.js you know

This version has breaking changes — APIs, conventions, and file structure may all differ from your training data. Read the relevant guide in `node_modules/next/dist/docs/` (resolved from this file's directory; in monorepos the `next` package may not be visible from the repo root) before writing any code. Heed deprecation notices.

This block is written and re-added by `next dev` — verify at `node_modules/next/dist/server/lib/generate-agent-files.js`. Removing it from a diff only re-creates the uncommitted change; committing it with your work keeps the tree clean.

<!-- END:nextjs-agent-rules -->
