# 0012 Project Structure

Date: 2026-10-12

## Status

Accepted. Replaces the layer-first sketch that `docs/ARCHITECTURE.md` carried
before this date.

## Context

Every project built from this harness needs the same, predictable answer to
"where does this code go?" — front end, back end, and database — so that agents
do not re-decide it on each task and humans can find things.

The earlier sketch split code by technical layer (`lib/domain`, `lib/data`,
`server/`). That scatters one product capability across several folders and says
nothing about how the front end, back end, and database relate. The harness now
serves two kinds of product: **content sites** (for example a restaurant) and
**internal apps** (for example attendance and employee management).

## Decision

1. **One deployable Next.js app** (a modular monolith). The front-end/back-end
   boundary is *code that runs in the browser* versus *code that runs only on
   the server*, enforced by imports — not by separate `frontend/` and
   `backend/` folders.
2. **Feature-first.** Top-level capability folders live in `features/<name>/`;
   layers (components, server, domain, schemas) live *inside* each feature.
3. **`app/` is routing only.** Pages are thin and compose features. They never
   call the database and hold no business rules.
4. **The database is its own module** (`db/`). Only feature repositories talk to
   it; components and pages never import it.
5. **Shared things stay shared:** UI primitives in `components/`,
   cross-cutting helpers in `lib/`, typed site content in `content/` (content
   sites only).
6. **A new top-level folder requires a decision record.**

The concrete layout, import matrix, data flow, naming, and the checklist for
adding a feature are in `docs/ARCHITECTURE.md`.

Enforcement: the first implementation story wires import-boundary lint rules
(ESLint `no-restricted-imports` or `eslint-plugin-boundaries`) and the
`server-only` guard, and they run inside `pnpm lint`. A rule that is only
written down is advice, not a constraint.

Tests: unit tests sit next to the code (`*.test.ts`); integration and
end-to-end tests live under `tests/`.

## Alternatives Considered

1. **Layer-first** (`lib/domain`, `lib/data`, `server/`) — rejected: one feature
   is spread over four places, and ownership is unclear.
2. **Separate `frontend/` and `backend/` apps or a monorepo** — rejected for now:
   a second runtime and deploy target, duplicated types, and CORS for no current
   benefit. Revisit when a trigger below applies.
3. **Full hexagonal / DDD per feature** — rejected: ceremony that these product
   sizes do not repay.

Split the back end out only when one of these is true: a non-web client (mobile
app, partner integration) needs the same API; long-running or scheduled jobs
need their own worker; parts must scale or deploy independently; or separate
teams own the front and back end. Keeping `domain/` pure makes that move cheap.

## Consequences

Positive:

- One place per capability; changing or deleting a feature touches one folder.
- Clear, mechanical boundaries; server code cannot leak into the browser bundle.
- The same layout serves content sites and internal apps.

Tradeoffs:

- Someone must decide what is "shared" versus feature-local; the default is
  feature-local until a second feature needs it.
- Needs lint configuration up front to be more than advice.
- Existing code that follows another layout needs a separate, tested refactoring
  story; do not move files wholesale without tests.

## Follow-Up

- First implementation story scaffolds the layout and wires the boundary lint.
- Record a new decision before adding a top-level folder or splitting the back
  end.
