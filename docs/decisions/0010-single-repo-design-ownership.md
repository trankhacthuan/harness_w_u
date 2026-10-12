# 0010 Single-Repo Design Ownership

Date: 2026-10-12

## Status

Accepted. Supersedes `0008-ui-ux-boundary.md`.

## Context

`0008` split ownership: this repo built behavior and accessibility, while a
separate repository (`UX_UI_HARNESS`) owned the visual system, and the two were
synced by hand through Codex. In practice that means two repos, two review
passes, and a manual hand-off before a screen can be called finished.

The goal is now to deliver a complete website — front end, back end, database,
and a distinctive visual design — from **this repository alone**, driven by one
brief and one agent loop.

## Decision

This repository owns the whole product:

| Concern | Where it lives |
| --- | --- |
| Product definition and brief | `docs/product/PRODUCT.md` |
| Content integrity (no fabricated facts) | `docs/CONTENT_RULES.md`, `docs/product/MISSING.md` |
| Art direction, tokens, motion, performance budget | `docs/DESIGN.md`, `app/styles/tokens.css` |
| Components and pages | `app/`, `components/` (CSS Modules) |
| Behavior, data, validation | `lib/`, `server/`, `db/` |
| Proof | the validation ladder in `docs/HARNESS.md` |

Rules carried over from `UX_UI_HARNESS` (they are the valuable part):

- WCAG 2.1 AA contrast, visible focus, 44px touch targets, keyboard operation.
- `prefers-reduced-motion` respected; motion explains, never decorates.
- Values come from tokens (CSS custom properties); components do not hard-code
  colors, sizes, or durations.
- Every async surface has loading, empty, and error states.

Not carried over: the 28 component specs, the DTCG token pipeline, the preset
style directions, and the cross-repo hand-off documents. A site's identity is
decided per brief, not picked from presets.

`UX_UI_HARNESS` stays available as an optional reference. There is no sync, no
package link, and nothing in this repo depends on it.

## Alternatives Considered

1. Keep `0008` (separate design repo) — rejected: it contradicts the goal of a
   single repo that takes a brief to a finished site.
2. Consume `UX_UI_HARNESS` as a submodule or package — rejected: adds release
   and versioning overhead to gain a generic preset system that per-site art
   direction deliberately avoids.

## Consequences

Positive:

- One loop from brief to finished site; no manual synchronization.
- Visual review becomes a gate of the same ladder as behavior.
- Per-site identity instead of preset-driven sameness.

Tradeoffs:

- No reusable cross-project design system; each site pays its own design cost.
- Design rules live next to code and can drift; `docs/DESIGN.md` is the guard.
- The agent now makes visual decisions, so they must be recorded and reviewed
  against evidence (see the design review gate in `docs/DESIGN.md`).

## Follow-Up

- First implementation story creates `app/styles/tokens.css` and the global
  reset, per `docs/DESIGN.md`.
- If a second site is built from this harness, revisit extracting a shared
  design kit.
