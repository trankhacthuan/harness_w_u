# 0009 Web Application Stack

Date: 2026-09-01

## Status

Accepted

## Context

The harness needs a locked default stack so architecture rules, the validation
ladder, and story templates can name real commands instead of placeholders.
The product is websites with standard (non-bespoke) UI/UX; visual design is
owned by a separate repo (see `0008-ui-ux-boundary.md`).

## Decision

Default stack:

| Concern | Choice |
| --- | --- |
| Framework | Next.js (App Router) |
| Language | TypeScript (strict) |
| Package manager | pnpm |
| Unit / component tests | Vitest + Testing Library |
| End-to-end tests | Playwright |
| Lint | ESLint (`next/core-web-vitals`) |
| Formatting | Prettier |
| Accessibility check | `@axe-core/playwright` in E2E, `eslint-plugin-jsx-a11y` in lint |
| Styling | Consumes the UI/UX repo's token / class convention; no bespoke design system here |
| Hosting | Deferred until the first deploy story |

The app is **not scaffolded yet**. Per Harness rules, run `create-next-app`
only when the first implementation story needs it.

## Alternatives Considered

1. Astro — lighter for content sites, but weaker fit if the product grows
   interactive surfaces. Revisit if the product stays mostly static.
2. Vite + React (no framework) — more setup for routing, data, SSR, SEO.

## Consequences

Positive:

- `docs/ARCHITECTURE.md`, the validation ladder, and `docs/templates/story.md`
  can reference concrete `pnpm` scripts.
- SSR/SEO/routing handled by the framework.

Tradeoffs:

- Next.js is heavier than a static-site generator if the product never needs
  interactivity.
- Locks Node tooling; changing later is a new decision.

## Follow-Up

- First implementation story scaffolds the app and wires the `pnpm` scripts
  named in `docs/ARCHITECTURE.md`.
- Record a deploy/hosting decision when the first deploy story is picked.
