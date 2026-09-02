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
| Integration | server actions, route handlers, data fetchers, external providers | `pnpm test` (integration project) |
| E2E | user-visible browser flows + accessibility | `pnpm test:e2e` (Playwright + axe) |
| Platform | production build, routing, deploy smoke | `pnpm build` |

## Required Checks Per Lane

| Lane | Checks |
| --- | --- |
| tiny | `pnpm lint` + `pnpm typecheck` on touched files |
| normal | `pnpm lint && pnpm typecheck && pnpm test`; add `pnpm test:e2e` when a user-visible flow changes |
| high-risk | full ladder: `... && pnpm test:e2e && pnpm build` |

## Matrix

| Story | Contract | Unit | Integration | E2E | Platform | Status | Evidence |
| --- | --- | --- | --- | --- | --- | --- | --- |
| TBD | Add rows when story packets are created | no | no | no | no | planned | none |

## Evidence Rules

- A story may ship without every proof column if its packet explains why.
- Accessibility (WCAG 2.1 AA baseline) and the loading/empty/error states of
  every async surface are part of E2E proof, not optional polish.
- Visual / brand review is **not** tracked here — it belongs to the UI/UX repo
  (`docs/decisions/0008-ui-ux-boundary.md`).
