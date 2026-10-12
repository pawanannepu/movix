# Movix

A responsive web app that learns your movie taste and gives you a personalized feed that explains each pick.

## Run it locally

You need Node 24 (the version in `.nvmrc`) and pnpm 10.

```
pnpm install
pnpm dev
```

Then open http://localhost:3000.

## Other commands

- `pnpm build` makes a production build.
- `pnpm start` serves that build.

## Where things live

- `src/app`: pages and layouts
- `src/db`: database client and schema (added with the data model)
- `src/auth`: sign in (added with the sign in feature)
- `src/lib`: shared helpers
- `scripts`: one off scripts, such as loading the movie catalog

The stack decision is in `docs/specs/0001-stack-and-architecture/index.md`. The plan is in `docs/scope/scope.md`.
