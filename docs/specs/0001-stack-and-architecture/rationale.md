# 0001. Stack and architecture: rationale

The build spec is [index.md](index.md). This file holds the reasoning, the options, and the evidence. `/develop` skips it.

## Context

Movix is a free, responsive web app that learns a person's taste from swipes or a Letterboxd ratings file, then serves a ranked feed with a reason per pick and a search that understands a mood. One person builds it over a few weeks, using the Skateboard approach: the thinnest usable whole first, then grow it release by release.

The forces that shape the stack:

- **Team and time.** One builder, a few weeks. Every extra service to run or learn comes out of product time.
- **Data shape.** Users, movies, ratings, swipe signals, and lists are clearly relational. Vibe search also needs vector similarity over movie text, so one store should hold both.
- **Request time work.** Ranking a feed, matching a ratings file to the catalog, and similarity search all run while a person waits. Hosting limits on CPU and time per request matter.
- **Interaction.** Swipe cards, keyboard and button alternatives, and WCAG AA contrast. A rich client UI on top of server rendered pages.
- **Sensitive data.** Email and password accounts and a person's full rating history. Auth must be done by a proven library, not by hand.
- **Cost and future.** Free for now, small scale, no billing. Monetization may come later, and vendor terms (hosting plan, movie data license) can change what is allowed.

Not deciding now means every feature spec after this one has to guess its tools, and the scaffold cannot start.

## Options considered

The options are whole stacks, not single tools.

### Option 1: One TypeScript monolith on Next.js, Postgres with pgvector on Neon, Drizzle, Better Auth, Vercel

A single Next.js app serves pages and server endpoints. Data and embeddings live in one managed Postgres. Auth is a library inside the app. Hosted on the framework vendor's platform.

**Pros**:
- One language, one repo, one deployable; shared types from database to UI.
- Plain Postgres means the data is portable and nothing locks you to a vendor.
- One store for relational data and vectors, no second system for vibe search.
- Largest ecosystem and the most readily available answers for a solo builder.

**Cons**:
- Next.js is a large framework with fast moving conventions (server components, caching rules).
- Neon's free plan scales to zero, so the first request after idle is slow, and storage is capped at 1 GB.
- Hosting free plan is meant for non commercial use, so monetizing means a plan change.

### Option 2: Supabase platform stack (Next.js plus Supabase Postgres, Auth, and Storage)

Use one platform for database, vectors, auth, and file storage, with Next.js on top.

**Pros**:
- Fewest separate accounts and services; auth and storage come bundled.
- pgvector is available.

**Cons**:
- More coupling to one vendor's auth and row level policies, harder to move later.
- Bundled storage is not needed yet (a ratings file is parsed and discarded), so part of the bundle goes unused.
- Auth data lives in the vendor's schema, which constrains data model choices.

### Option 3: Web app plus a separate Python service for embeddings and ranking

The web app handles pages and accounts. A Python service owns embeddings, similarity, and ranking.

**Pros**:
- Best machine learning ecosystem if ranking becomes custom model work.

**Cons**:
- Two runtimes, two deployments, and a network hop on every recommendation, for a product whose first release is a ranked list from a profile.
- The extra moving part is paid for on day one, and the need is hypothetical.

### Option 4: Edge first stack on Cloudflare Workers

A framework deployed to Workers with an edge database.

**Pros**:
- Low latency worldwide, and a generous free request allowance.

**Cons**:
- The free plan gives 10 ms of CPU per request, which is tight for parsing a ratings file or ranking.
- Vector search and Node library support need extra care at the edge.
- Adds adapter and runtime differences for a solo builder who gains nothing from edge placement yet.

## Rationale

Option 1 fits the forces most directly. A monolith is the right pattern for one builder and a small user count: it is the easiest to build, debug, and change, and nothing needs extracting until a real bottleneck appears. Postgres is the right default for relational data, and pgvector lets vibe search live in the same database, so there is no second system. TypeScript end to end keeps the swipe UI, server code, and database types in one language.

Better Auth keeps accounts inside the app and the database, which matches the choice to avoid a vendor holding user records, and it avoids rolling auth by hand. The advisory fixed in 1.6.22 affects plugins this product does not use, but the version is pinned at or above it anyway.

Hosting on Vercel removes adapters and gives enough time per request for CSV parsing and ranking, which the Workers free plan would squeeze. The cost is the plan terms: acceptable while Movix is free, and flagged for revisit before any monetization.

Deferring the catalog source, embedding model, reasons model, and component library to their own feature specs keeps this spec to one decision. The stack only guarantees that vector search and a server runtime are available to them.

Runner up: Option 2. It would be the pick if you wanted bundled auth and file storage and were comfortable with the coupling.

## Landscape scan (evidence)

Checked 2026-10-12 by a read only research pass on the cheapest model. Items it could not confirm are marked unverified and were not relied on.

| Area | Finding | Status |
|---|---|---|
| Next.js | 16.3 is the latest minor (Aug 3, 2026), Turbopack is the default since 16 | Confirmed |
| Neon | pgvector on the free plan, 1 GB storage per project, scale to zero after 5 minutes idle, no sunset found | Confirmed |
| Supabase | pgvector available as an extension | Confirmed |
| Better Auth | latest stable 1.7.7 (Sep 30, 2026), advisory fixed in 1.6.22 and 1.7.0 beta 10 for the magic link and email code plugins | Confirmed |
| Cloudflare Workers | free plan 100,000 requests a day and 10 ms CPU per invocation, paid from $5 a month | Confirmed |
| TanStack Start | reported 1.0 in 2026 by secondary sources only | Unverified |
| SvelteKit, React Router v7, Nuxt | status not checked this run | Unverified |
| Clerk, Auth.js, Lucia | not checked this run | Unverified |
| Vercel plan terms, Fly.io, Railway | not checked this run | Unverified |
| Drizzle, Prisma, Zod versions | not checked this run | Unverified |
| TMDB | free for non commercial use with attribution, commercial use needs a license | Confirmed (relevant to feature #4, not decided here) |

## References

**Project sources**:
- Scope row 1, Stack & architecture, in `docs/scope/scope.md`
- Build approach: Skateboard, from the scope header

**Practices & standards**:
- Monolith first until a real bottleneck forces a split
- A relational database as the default primary store
- Use a proven auth library, never hand rolled auth
- One decision per spec, so catalog, embeddings, and reasons are decided in their own specs

**Links** (web verified in the Stage (c) landscape check):
- Next.js blog (release status): https://nextjs.org/blog?page=1
- Neon pricing and free plan limits: https://neon.com/pricing
- Supabase pgvector docs: https://supabase.com/docs/guides/database/extensions/pgvector
- Better Auth releases: https://github.com/better-auth/better-auth/releases
- Better Auth advisory CVE-2026-67327: https://corgea.com/advisories/vulnerabilities/CVE-2026-67327
- Cloudflare Workers pricing: https://developers.cloudflare.com/workers/platform/pricing/
- TMDB API FAQ (terms of use): https://developer.themoviedb.org/docs/faq
