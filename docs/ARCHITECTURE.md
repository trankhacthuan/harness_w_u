# Architecture

Stack is locked in `docs/decisions/0009-web-stack.md` and
`docs/decisions/0011-postgresql-database.md`:
**Next.js (App Router) + TypeScript (strict) + pnpm**, CSS Modules + CSS
variables, **PostgreSQL** via Drizzle ORM, tests with Vitest + Playwright.

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
- Validation ladder: the smallest checks that prove the change (see below).

Record a decision in `docs/decisions/` when a choice meaningfully constrains
future work (new external provider, auth model, rendering strategy shift, data
store).

## Project Shape (target)

```text
app/                      Next.js App Router routes
  (marketing)/            route groups
  <route>/
    page.tsx
    loading.tsx
    error.tsx
    not-found.tsx
  styles/
    tokens.css            design tokens (CSS custom properties)
    global.css            reset and base styles
components/               presentational components (CSS Modules)
lib/
  domain/                 pure product types and rules (framework-free)
  data/                   repositories over the database, parse at the edge
  validation/             zod (or equivalent) schemas for boundary input
db/
  schema.ts               Drizzle schema
  migrations/             forward-only SQL migrations (committed)
server/                   route handlers, server actions
tests/
  unit/                   Vitest
  integration/            Vitest against a throwaway PostgreSQL
  e2e/                    Playwright (+ @axe-core/playwright)
```

Create a folder only when a story needs it.

## Layering And Dependency Rule

```text
domain  <-  data / validation  <-  server (actions, route handlers)  <-  app routes / components
```

Inner layers must not depend on outer layers.

| Layer | May depend on | Must not depend on |
| --- | --- | --- |
| `lib/domain` | tiny pure utilities only | Next.js, React, fetch, env, DB clients |
| `lib/data`, `lib/validation`, `db` | `lib/domain` | React components, route handlers |
| `server` | domain, data, validation | React component internals, browser APIs |
| `app` routes / `components` | server contracts, domain types | direct DB access, secrets |

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
- Never import server-only modules (DB, secrets, `server/`) into Client
  Components. Pass data down as serializable props.
- Mutations go through Server Actions or route handlers, which own validation
  and any audit side effects.

## Database

Rules are in `docs/decisions/0011-postgresql-database.md`. In short: forward-only
reviewed migrations, `DATABASE_URL` validated lazily on first use (never at
startup or import), parameterized access only, least-privilege role, no personal
data in logs, tests against a real PostgreSQL instance. Only `lib/data` and `db`
talk to the database; route handlers call repositories, never SQL.

Degradation: pages that need no data neither import the database module nor
query it at build time. If the database is missing or unreachable, only the
features that need it are disabled — the API answers `503` and the reservation
form shows a message plus a call button — and the rest of the site keeps
working.

## Rendering And SEO

- Choose the least dynamic mode that works: static > ISR > SSR > client.
- Every indexable route exports `metadata` (title, description, canonical, OG).
- No layout shift from client-only data: render a skeleton via `loading.tsx`.

## Observability Contract

Server route handlers emit one canonical JSON log line per request:
`timestamp, level, request_id, user_id?, route, method, duration_ms,
status_code, message`. Application logs are operational records and are separate
from any product/audit records.
