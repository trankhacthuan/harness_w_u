# 0008 UI/UX Ownership Boundary

Date: 2026-09-01

## Status

Accepted

## Context

This harness is used to build websites. Visual design (layout language, color,
type scale, spacing, motion, component look and feel) is owned by a **separate
UI/UX repository**. That repo needs to know what it is styling before it can
choose an appropriate style.

Without a boundary, agents working in this repo drift into bespoke styling
decisions that then conflict with the UI/UX repo, and the UI/UX repo has no
single source describing the product it is dressing.

## Decision

Split ownership:

| This repo (`harness_w_u`) | The UI/UX repo |
| --- | --- |
| Product definition (`docs/product/PRODUCT.md`) | Visual language, design tokens, component styling |
| Routes, pages, data flow, state (loading / empty / error) | Look and feel of those states |
| Semantic, accessible markup | CSS, theme, motion, brand expression |
| Functional + a11y + responsive-structure validation | Visual review |

Interface between the two repos:

1. `docs/product/PRODUCT.md` in this repo is the **canonical product brief**.
   The UI/UX repo reads it (delivered manually via Codex) to choose a style
   direction.
2. Functional code produced here is delivered to the UI/UX repo the same way
   (manual upload). There is no npm package, submodule, or monorepo link.
3. Styling flows back as design tokens / class conventions / component specs
   that this repo consumes without re-deriving them.

Acceptance criteria for a screen in this repo:

- Behavior matches the story contract.
- Markup is semantic and keyboard-accessible (WCAG 2.1 AA baseline).
- Layout is responsive at structural level (no fixed pixel widths that break
  small screens).
- Every async surface has explicit loading, empty, and error states.
- Styling uses whatever token / class convention the UI/UX repo defined; it does
  **not** invent a new one.

Not in scope here: visual polish, brand consistency, pixel-level design review.
Those are the UI/UX repo's acceptance gates.

## Alternatives Considered

1. Single repo owning both function and visual design — rejected: the user
   explicitly wants a dedicated UI/UX repo and a reusable style system.
2. Consume the UI/UX repo as an npm package / git submodule — rejected for now:
   the user's workflow is manual file delivery through Codex; a hard dependency
   adds release/versioning overhead not yet needed.

## Consequences

Positive:

- One canonical product description for the UI/UX repo to design against.
- Agents here stop making conflicting visual decisions.
- Feature intake for "normal UI/UX" work stays cheap: no visual-design gate.

Tradeoffs:

- Manual sync between repos; drift is possible if `PRODUCT.md` is not kept
  current.
- Two review passes (function here, visuals there) for a finished screen.

## Follow-Up

- Keep `docs/product/PRODUCT.md` updated whenever product scope or tone changes.
- If manual sync becomes painful, record a follow-up decision to formalize the
  interface (package or shared tokens file).
