# Architecture

Stack is locked in `docs/decisions/0009-web-stack.md`:
**Next.js (App Router) + TypeScript (strict) + pnpm**, tests with
Vitest + Playwright.

The app is **not scaffolded yet**. Create real folders and run `create-next-app`
only when the first implementation story needs them.

## UI/UX Boundary

Visual design lives in a separate repository (`docs/decisions/0008-ui-ux-boundary.md`).
In this repo:

- Write semantic, accessible markup. Structure and behavior are the deliverable.
- Do not invent a design system, color palette, spacing scale, or component
  look. Consume whatever token / class convention the UI/UX repo provides.
- Every async surface ships explicit `loading`, `empty`, and `error` states.
- Layout is responsive at the structural level (fluid containers, no fixed pixel
  widths that break at 320px).

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
components/                presentational components (styled per UI/UX repo)
lib/
  domain/                 pure product types and rules (framework-free)
  data/                   fetchers / repositories, parse at the edge
  validation/             zod (or equivalent) schemas for boundary input
server/                   route handlers, server actions
tests/
  unit/                   Vitest
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
| `lib/data`, `lib/validation` | `lib/domain` | React components, route handlers |
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

## Rendering And SEO

- Choose the least dynamic mode that works: static > ISR > SSR > client.
- Every indexable route exports `metadata` (title, description, canonical, OG).
- No layout shift from client-only data: render a skeleton via `loading.tsx`.

## Observability Contract

Server route handlers emit one canonical JSON log line per request:
`timestamp, level, request_id, user_id?, route, method, duration_ms,
status_code, message`. Application logs are operational records and are separate
from any product/audit records.
