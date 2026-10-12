# Architecture

Stack is locked in `docs/decisions/0009-web-stack.md` and
`docs/decisions/0011-postgresql-database.md`:
**Next.js (App Router) + TypeScript (strict) + pnpm**, CSS Modules + CSS
variables, **PostgreSQL** via Drizzle ORM, tests with Vitest + Playwright.
Project layout is locked in `docs/decisions/0012-project-structure.md`.

The app is **not scaffolded yet**. Create real folders and run `create-next-app`
only when the first implementation story needs them.

## Design And Content

This repository owns the visual design too
(`docs/decisions/0010-single-repo-design-ownership.md`). The rules live in
`docs/DESIGN.md` and `docs/CONTENT_RULES.md`. For architecture purposes:

- Styling is CSS Modules reading tokens from `app/styles/tokens.css`. No raw
  design values inside components.
- Every async surface ships explicit `loading`, `empty`, and `error` states.
- Layout is mobile-first and responsive (fluid containers, no fixed pixel
  widths that break at 320px).
- Motion is CSS plus `IntersectionObserver`; only `transform` and `opacity`
  animate.
- Content gaps are visible placeholders tracked in `docs/product/MISSING.md`.

## Discovery Before Shape

Before proposing implementation shape for a feature, identify:

- Routes and rendering mode per route: static, SSR, ISR, or client.
- Data sources: internal route handlers, external APIs, CMS, database.
- Boundary inputs: form data, search params, route params, cookies/session,
  environment variables, webhook payloads, third-party SDK responses.
- Interactivity: which parts are Server Components vs Client Components.
- SEO needs: which routes must be indexable and carry metadata.
- Which feature module owns the capability (or whether a new one is needed).
- Validation ladder: the smallest checks that prove the change (see below).

Record a decision in `docs/decisions/` when a choice meaningfully constrains
future work (new external provider, auth model, rendering strategy shift, data
store, new top-level folder).

## One App, Three Concerns

This is a single Next.js app. Front end, back end, and database are separated by
**where code runs and what it may import**, not by separate folders or packages.

| Concern | Lives in | Runs on |
| --- | --- | --- |
| UI and interaction | `app/` pages, `features/*/components`, `components/` | browser and server render |
| Business rules | `features/*/domain` | anywhere (pure code) |
| Use cases, authorization, orchestration | `features/*/server` | server only |
| Data access | `features/*/server/repository.ts`, `db/` | server only |
| Schema and migrations | `db/` | build and deploy time |
| Configuration | `lib/env` (validated lazily) | server only |

## Project Shape

```text
app/                          Routing only. Thin pages compose features.
  (public)/                   Marketing / content routes (content sites)
  (auth)/login/               Sign-in routes (when authentication exists)
  (app)/<area>/               Authenticated product areas (internal apps)
  api/<endpoint>/route.ts     Public HTTP endpoints only (webhooks, external clients)
  styles/
    tokens.css                Design tokens (CSS custom properties)
    global.css                Reset and base styles
  layout.tsx  error.tsx  not-found.tsx

features/<name>/              One folder per product capability
  components/                 UI for this feature (CSS Modules)
  server/                     Server-only code (see below)
    actions.ts                'use server' entry points; the only server file
                              client code may import
    queries.ts                Reads for pages (import 'server-only')
    service.ts                Use cases: authorize, validate, orchestrate
    repository.ts             The only code that talks to the database
    index.ts                  Server-only public surface of the feature
  domain/                     Pure rules and types: no framework, db, or env
  schemas.ts                  Boundary schemas (zod) for this feature
  index.ts                    Client-safe public surface (components, types)

components/                   Shared UI primitives (Button, Table, Tabs, Dialog…)
content/                      Typed site copy and data (content sites only)
lib/                          Cross-cutting: env (lazy), errors, logger,
                              time/timezone helpers, pure utilities

db/
  client.ts                   Lazy connection (never connects at import)
  schema/                     Drizzle tables, one file per feature
  migrations/                 Forward-only SQL migrations (committed)
  seed.ts                     Demo data only

tests/
  integration/                Vitest against TEST_DATABASE_URL
  e2e/                        Playwright (+ @axe-core/playwright)
```

Unit tests sit next to the code they cover (`late.test.ts` beside `late.ts`).
Create a folder only when a story needs it.

### Which folders a project uses

| Product type | Uses | Usually skips |
| --- | --- | --- |
| Content site (restaurant, brochure) | `app/(public)`, `content/`, `components/`, one or two small features (for example reservations) | `(app)`, most of `server/` |
| Internal app (attendance, HR) | `app/(auth)`, `app/(app)`, several `features/`, `db/` | `content/` |

## Dependency Rule

Imports flow inward. A module may import only what its row allows.

| From | May import | Must not import |
| --- | --- | --- |
| `app/` | `features/*/index.ts`, `features/*/server/index.ts`, `components/`, `lib/` | feature internals, `db/` |
| `features/X/components` | `X/domain`, `X/schemas`, `X/server/actions`, `components/`, `lib/`, other features' `index.ts` | `X/server` (except `actions`), `db/` |
| `features/X/server` | `X/domain`, `X/schemas`, `db/`, `lib/`, other features' `server/index.ts` | `components/`, `app/`, other features' internals |
| `features/X/domain` | pure utilities only | everything else (framework, db, env, fetch) |
| `components/` | `lib/` | `features/`, `db/` |
| `lib/` | nothing project-specific | `features/`, `db/`, `components/` |
| `db/` | Drizzle, `lib/` | `features/` |

Cross-feature access goes only through the other feature's public surface
(`index.ts` for client-safe things, `server/index.ts` for server-only things),
never through its internals.

## Data Flow

```text
Read:    page (server component) -> features/X/server/queries -> repository -> db
                                 -> typed DTO -> component props

Write:   client form -> server action (actions.ts) -> parse with schemas.ts
                     -> authorize -> service (use case) -> repository -> db
                     -> typed result (ok | error) -> UI state

Public:  api/<endpoint>/route.ts -> the same service used by the action
```

Rules that follow from the flow:

1. **Authorize on the server**, in the use case, for every read and write.
   Hiding a button is not authorization.
2. **Business rules live in `domain/`**, never in components, pages, or route
   handlers.
3. **Repositories return domain-shaped DTOs**, never raw rows, so sensitive
   columns cannot leak by accident.
4. **Pages and components never import `db/`.**
5. **Prefer Server Actions for in-app mutations**; use `api/` route handlers only
   for endpoints that outside clients call.

## Parse-First Boundary Rule

Unknown data is parsed into typed values at the boundary before it reaches inner
code.

Boundaries: form submissions and server-action args, `searchParams` / route
params, cookies and session/identity claims, environment variables, responses
from external APIs and SDKs, webhook payloads, database rows from external
clients.

```text
unknown input  ->  schema parse (zod/valibot)  ->  typed DTO  ->  server action / use case  ->  domain value
```

Inner code works with meaningful types (`Slug`, `UserId`, `Locale`, `DateRange`)
rather than re-validating raw strings.

## Server / Client Split

- Default to Server Components. Add `"use client"` only for interactivity
  (state, effects, event handlers, browser APIs).
- Server-only modules start with `import 'server-only'`. Client Components never
  import them; they may import `server/actions.ts` and nothing else from
  `server/`. Pass data down as serializable props.
- Mutations go through Server Actions or route handlers, which own validation
  and any audit side effects.

## Database

Rules are in `docs/decisions/0011-postgresql-database.md`. In short: forward-only
reviewed migrations, `DATABASE_URL` validated lazily on first use (never at
startup or import), parameterized access only, least-privilege role, no personal
data in logs, tests against a real PostgreSQL instance. Only
`features/*/server/repository.ts` and `db/` talk to the database; handlers and
use cases call repositories, never SQL.

Degradation: pages that need no data neither import the database module nor
query it at build time. If the database is missing or unreachable, only the
features that need it are disabled — the API answers `503` and the form shows a
message plus a fallback action — and the rest of the site keeps working.

## Naming

| Thing | Convention |
| --- | --- |
| Folders and non-component files | `kebab-case` |
| React component files and their CSS | `PascalCase.tsx` and `PascalCase.module.css` |
| Tests | `<name>.test.ts(x)` beside the code; `*.spec.ts` for Playwright |
| Database tables and columns | `snake_case`, plural table names |
| Feature names | singular nouns for capabilities (`attendance`, `employees`, `auth`) |

## Adding A Feature

1. Intake and, for personal data or auth, a high-risk story
   (`docs/FEATURE_INTAKE.md`).
2. Create `features/<name>/` with only the subfolders it needs.
3. If it stores data: `db/schema/<name>.ts` and a reviewed migration.
4. `domain/` rules with unit tests first.
5. `server/` use cases and repository, with integration tests.
6. `components/` using tokens; add the route in `app/`.
7. E2E and accessibility checks; update `docs/product/PRODUCT.md` and
   `MISSING.md`.

## Rendering And SEO

- Choose the least dynamic mode that works: static > ISR > SSR > client.
- Every indexable route exports `metadata` (title, description, canonical, OG).
- No layout shift from client-only data: render a skeleton via `loading.tsx`.

## Observability Contract

Server route handlers emit one canonical JSON log line per request:
`timestamp, level, request_id, user_id?, route, method, duration_ms,
status_code, message`. Application logs are operational records and are separate
from any product/audit records.
