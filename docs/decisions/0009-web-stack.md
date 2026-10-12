# 0009 Web Application Stack

Date: 2026-09-01

## Status

Accepted. Amended 2026-10-12 by `0010-single-repo-design-ownership.md`
(styling and design ownership) and `0011-postgresql-database.md` (database).

## Context

The harness needs a locked default stack so architecture rules, the validation
ladder, and story templates can name real commands instead of placeholders.
The product is a complete website — front end, back end, database, and a
distinctive visual design — built from this repository alone.

## Decision

Default stack:

| Concern | Choice |
| --- | --- |
| Framework | Next.js (App Router) |
| Language | TypeScript (strict) |
| Package manager | pnpm |
| Styling | CSS Modules + CSS custom properties (`app/styles/tokens.css`); no Tailwind, no CSS-in-JS runtime |
| Motion | CSS transitions/animations + `IntersectionObserver`; no animation library by default |
| Fonts | `next/font` (self-hosted), Vietnamese-capable |
| Database | PostgreSQL (`0011-postgresql-database.md`) |
| Data access | Drizzle ORM + `pg`; SQL migrations in `db/migrations/` |
| Unit / component tests | Vitest + Testing Library |
| Integration tests | Vitest against a throwaway PostgreSQL instance |
| End-to-end tests | Playwright |
| Lint | ESLint (`next/core-web-vitals`) |
| Formatting | Prettier |
| Accessibility check | `@axe-core/playwright` in E2E, `eslint-plugin-jsx-a11y` in lint |
| Performance check | Lighthouse against the budget in `docs/DESIGN.md` |
| Hosting | Deferred until the first deploy story |

The app is **not scaffolded yet**. Per Harness rules, run `create-next-app`
only when the first implementation story needs it.

## Alternatives Considered

1. Astro — lighter for content sites, but weaker fit once a back end and forms
   are involved.
2. Vite + React (no framework) — more setup for routing, data, SSR, SEO.
3. Tailwind for styling — faster to assemble, but uniform utility classes make
   per-site art direction harder to keep distinct.
4. Plain HTML/CSS/JS — lightest, but no back end in the same repo and no image
   or SEO tooling.

## Consequences

Positive:

- `docs/ARCHITECTURE.md`, the validation ladder, and `docs/templates/story.md`
  can reference concrete `pnpm` scripts.
- SSR/SEO/routing/image optimization handled by the framework; back end and
  front end live in one codebase.

Tradeoffs:

- Next.js is heavier than a static-site generator if a project never needs a
  back end.
- Locks Node tooling; changing later is a new decision.
- Hand-written CSS takes more effort than utility classes.

## Follow-Up

- First implementation story scaffolds the app and wires the `pnpm` scripts
  named in `docs/ARCHITECTURE.md` and `docs/HARNESS.md`.
- Record a deploy/hosting decision when the first deploy story is picked.
