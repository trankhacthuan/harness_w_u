# Test Matrix

Maps product behavior to proof. The durable version is
`scripts/bin/harness-cli query matrix` (`.exe` on Windows); this file explains
the columns and the per-lane check requirements.

No product behavior is implemented yet. Do not mark a row implemented until the
listed commands exist and have been run with output captured.

## Status Values

| Status | Meaning |
| --- | --- |
| planned | Accepted as intended behavior, not implemented |
| in_progress | Actively being built |
| implemented | Implemented and proof exists |
| changed | Contract changed after earlier implementation |
| retired | No longer part of the product contract |

## Proof Columns

| Column | Covers | Command (once scaffolded) |
| --- | --- | --- |
| Unit | pure domain / validation / component logic | `pnpm test` (Vitest) |
| Integration | route handlers, repositories, migrations against a real PostgreSQL | `pnpm db:migrate:test && pnpm test:integration` |
| E2E | user-visible browser flows + accessibility (375px, desktop, reduced motion) | `pnpm test:e2e` (Playwright + axe) |
| Platform | performance budget, production build, deploy smoke | `pnpm test:perf`, `pnpm build` |

## Required Checks Per Lane

| Lane | Checks |
| --- | --- |
| tiny | `pnpm lint` + `pnpm typecheck` on touched files |
| normal | `pnpm lint && pnpm typecheck && pnpm test`; add `pnpm test:e2e` when a user-visible flow changes; `design:review` when appearance changes |
| high-risk | full ladder (`test:release` in `docs/HARNESS.md`) + `design:review` |

## Matrix

| Story | Contract | Unit | Integration | E2E | Platform | Status | Evidence |
| --- | --- | --- | --- | --- | --- | --- | --- |
| TBD | Add rows when story packets are created | no | no | no | no | planned | none |

## Evidence Rules

- A story may ship without every proof column if its packet explains why.
- Accessibility (WCAG 2.1 AA baseline) and the loading/empty/error states of
  every async surface are part of E2E proof, not optional polish.
- Visual review is part of the proof: the six-question design review in
  `docs/DESIGN.md` is answered with cited evidence for every page or component
  that changes appearance.
- Content integrity (`docs/CONTENT_RULES.md`) is checked, and
  `docs/product/MISSING.md` must match the placeholders in the product.
