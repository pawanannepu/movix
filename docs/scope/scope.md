# Scope: Movix

A responsive web app that learns your movie taste from Letterboxd ratings or a quick swipe session, then gives you a personalized feed that explains each pick, plus a search that understands a mood.

**Build approach:** Skateboard (ship the thinnest usable whole first, then grow it release by release, each one shippable).
**Workflow:** Beta (after develop, `/check verify` then `/test`). The project default level of rigor. `/architect` is the recommended first stop for a feature with a real decision, but skippable when you already know the build. Any feature can carry its own tag (for example `· GA`) to do more or less.

_These are recommendations to keep your build orderly, not requirements. Skip anything that does not fit: if you already know how to build a feature, use `/develop` and skip `/architect`. You decide when a feature is `done`._

## At a glance

| # | Feature | Phase | Status |
|---|---------|-------|--------|
| 1 | Stack & architecture | Foundation | in-progress |
| 2 | Coding standards & tooling | Foundation | planned |
| 3 | Data model | Foundation | planned |
| 4 | Movie catalog & metadata | Foundation | planned |
| 5 | Design system & UI foundation | Foundation | planned |
| 6 | Sign in with email and password | Release 1 | planned |
| 7 | Swipe onboarding | Release 1 | planned |
| 8 | Taste profile | Release 1 | planned |
| 9 | Personalized feed | Release 1 | planned |
| 10 | Analytics & error monitoring | Release 1 | planned |
| 11 | Recommendation reasons | Release 2 | planned |
| 12 | Letterboxd CSV import | Release 3 | planned |
| 13 | Vibe search | Release 4 | planned |
| 14 | Watchlist & seen list | Release 5 | planned |
| 15 | Where to watch | Release 6 | planned |

## Foundations

### 1. Stack & architecture · in-progress
Decide the stack for a responsive web app with accounts, a movie catalog, a recommendation engine, and search. Scaffold only the runnable skeleton the first release needs.
**Done when:** the stack is recorded in a spec and the empty scaffold boots locally and passes build.
- [x] Decide the stack (spec): `/architect stack & architecture`
- [x] Scaffold from the decision: `/develop stack & architecture`
Spec [0001](../specs/0001-stack-and-architecture/index.md) · code in [src/](../../src/)

### 2. Coding standards & tooling
Capture conventions from the real scaffold, then install lint, format, and pre-commit enforcement so all later code follows them.
**Done when:** root `AGENTS.md` reflects the real stack, and lint, format, and pre-commit run clean.
- [ ] Capture conventions and tooling choices: `/audit`
- [ ] Install the tooling: `/develop tooling`
- [ ] Check it runs clean: `/test`

### 3. Data model · needs a decision
The core entities every feature builds on: users, movies, ratings, swipe signals, taste profile, and saved lists. A wrong data model is the costliest thing to redo, so it comes before any feature.
**Done when:** the entities and relationships support onboarding, import, the feed, search, and lists without a breaking migration, and a person has at most one rating per movie.
- [ ] Design it (spec): `/architect data model`

### 4. Movie catalog & metadata · needs a decision
Where movies and their details (title, year, genres, synopsis, cast, poster, keywords) come from and how they get into the app, so swipe cards, the feed, and search all draw from one catalog.
**Done when:** the app holds a catalog with enough detail per movie to power swipe cards, feed items, and later mood search, and a movie can be looked up by title and year.
- [ ] Design it (spec): `/architect movie catalog & metadata`

### 5. Design system & UI foundation · needs a decision
Visual language, layout primitives, and base components, including a swipe card, so every screen feels cohesive and accessible from the first one.
**Done when:** `design.md` covers type, color, spacing, and components, base components handle focus and keyboard, and text and controls meet WCAG AA contrast.
- [ ] Design it (spec): `/architect design system & UI foundation`

## Release 1: Smallest usable whole

A person can create an account, swipe to teach the app their taste, and see a personalized feed. Nothing else yet.

### 6. Sign in with email and password · needs a decision · GA
Create an account, sign in, sign out, and stay signed in. Tagged `GA` because it stores passwords and personal data.
**Done when:** a person can sign up, sign in, and sign out, stays signed in across visits, passwords are never stored in plain text, and signed out visitors cannot reach personal pages.
- [ ] Design it (spec): `/architect sign in with email and password`

### 7. Swipe onboarding · needs a decision
New people swipe through a varied deck of movies (like, dislike, not seen, skip) to teach the app their taste, then land on their first feed.
**Done when:** a new user can swipe by touch, mouse, or keyboard and buttons, every swipe is saved, they can leave and resume where they stopped, and after enough swipes they reach the feed.
- [ ] Design it (spec): `/architect swipe onboarding`

### 8. Taste profile · needs a decision
Turn ratings and swipes into one taste profile that the feed reads from. Every way of giving taste, now and later, writes into it.
**Done when:** a user with signals has a profile that updates as they swipe, a brand new user gets a sensible fallback, and the profile can be rebuilt from the stored signals.
- [ ] Design it (spec): `/architect taste profile`

### 9. Personalized feed · needs a decision
A ranked list of movies picked for this person from their taste profile. This is the product's main screen.
**Done when:** a signed in user with a profile sees a ranked feed of movies they have not rated or swiped, items do not repeat, refreshing gives new picks, and loading, empty, and error states render.
- [ ] Design it (spec): `/architect personalized feed`

### 10. Analytics & error monitoring · needs a decision
Measure the number that matters (people who reach a personalized feed) and see errors before users report them.
**Done when:** signed up, finished onboarding, and saw first feed are each measurable, and browser and server errors are captured with enough context to act on, without sending passwords or personal details.
- [ ] Design it (spec): `/architect analytics & error monitoring`

## Release 2: Explain the picks

### 11. Recommendation reasons · needs a decision
Every feed pick says why you are seeing it, in a sentence tied to your own taste.
**Done when:** each feed item shows a reason grounded in the user's real ratings or swipes, never an invented one, and a pick with no strong reason falls back to a plain honest line.
- [ ] Design it (spec): `/architect recommendation reasons`

## Release 3: Bring your history

### 12. Letterboxd CSV import · needs a decision
Upload your Letterboxd ratings file and skip most of the swiping. The matched ratings go straight into your taste profile.
**Done when:** a user uploads a ratings file, sees how many films matched and how many did not, confirms, and the ratings land in the taste profile; a bad or empty file shows a clear error; uploading the same file again does not duplicate ratings.
- [ ] Design it (spec): `/architect letterboxd csv import`

## Release 4: Search by vibe

### 13. Vibe search · needs a decision
Search by feeling in plain words ("slow, melancholic road movie about grief"), not only by title.
**Done when:** a user types a mood or description and gets relevant movies, plain title search still works, and empty and error states render.
- [ ] Design it (spec): `/architect vibe search`

## Release 5: Keep track

### 14. Watchlist & seen list
Save movies for later and mark ones you have already seen, from the feed and from search. Seen movies leave the feed.
**Done when:** a user can add and remove a movie from the watchlist, mark one seen, view both lists, and seen movies stop appearing in the feed.
- [ ] Build it: `/develop watchlist & seen list`

## Release 6: Where to watch

### 15. Where to watch · needs a decision
Show which streaming services carry a movie in the user's country.
**Done when:** a movie shows the services that carry it for the user's country or says it is not available, and unknown availability is shown honestly rather than guessed.
- [ ] Design it (spec): `/architect where to watch`

## Deferred
Out of scope for the current build pass, kept so the plan stays honest.
- **Password reset**: recover an account by email · needs a decision (worth pulling forward before real users arrive)
- **Edit and manage ratings**: see, change, and delete your ratings after onboarding
- **Google sign in and email magic link**: more ways to sign in · needs a decision
- **Privacy policy, terms, and delete my data**: people upload files and ratings, so they should be able to see and remove them · needs a decision (worth doing before a public launch)
- **Public movie pages for search engines**: indexable pages with metadata · needs a decision
- **Sharing and taste comparison**: share a pick or compare taste with friends · needs a decision

## Legend

**The decision box.** Every feature that needs one carries exactly one, the sub task whose label ends with `(spec)`. Its wording varies (`Design it (spec)` normally, `Decide the stack (spec)` on Stack & architecture), so skills locate it by that `(spec)` suffix. Every other box is an execution box and `/architect` never ticks one.

**Feature lifecycle**:

| State | Set by | The feature shows |
|---|---|---|
| `planned` · needs a decision | `/scope` | one box: `Design it (spec): /architect <feature>` |
| `in-progress` (designed) | `/architect` at spec capture | `Design it` ticked, spec linked, `Build it: /develop <feature>` with 2 to 5 milestones, then `Verify it` and `Test it` for this tier |
| `in-progress` (building) | `/develop` | milestone boxes tick one by one, code pointer filled |
| `in-progress` (verified) | `/check verify` | `Build it` and milestones ticked, `Verify it` ticked |
| `done` | you, when you decide it is | boxes you ran ticked, skipped ones marked skipped. At `Beta` the suggested point is after `/test` |

- **Next step** is the first unticked box, always a command or a tracked milestone.
- **needs a decision** means run `/architect` first, otherwise straight to `/develop` (or `/audit` for standards & tooling). The tag drops once the spec is captured.
- **Atomic build tasks** live in the spec's `## Build plan`, not here. The scope carries only the milestone rollup.
- **Status** moves `planned` to `in-progress` to `done`, plus `existing` (built before the workflow) and `dropped` (de scoped, kept for history).
- **Approach tag** beside a heading (for example `· Facade`) overrides the project default for that feature. No tag means it inherits.
- **Workflow tier tag** beside a heading (for example `· GA`) sets that feature's rigor above or below the project default. No tag means it inherits.
- **Workflow** (header line) is the project default for what runs after `/develop`: `Prototype` is nothing, `Alpha` is `/check verify`, `Beta` is `/check verify` then `/test`, `GA` adds a fresh model `/check review` then `/document`.
- **Pointer line** (`spec <n> · code in <path>`) appears once a spec or code exists: the spec link is added by `/architect`, the code path by `/develop`.
