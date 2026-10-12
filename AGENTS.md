# Agent Instructions

## What this repo is

`harness_w_u` is a repository harness for building **complete websites** from a
single repository: product brief, visual design, front end, back end, database,
and validation (`docs/decisions/0010-single-repo-design-ownership.md`). There is
no separate design repository to wait on.

Stack: Next.js (App Router) + TypeScript + pnpm, CSS Modules + CSS variables,
PostgreSQL via Drizzle (`docs/decisions/0009-web-stack.md`,
`docs/decisions/0011-postgresql-database.md`). The app is not scaffolded until
the first implementation story needs it.

## Before work, read

- `README.md`
- `docs/HARNESS.md`
- `docs/FEATURE_INTAKE.md`
- `docs/ARCHITECTURE.md`
- `docs/CONTEXT_RULES.md`
- `docs/product/PRODUCT.md` — the product brief
- `docs/DESIGN.md` and `docs/CONTENT_RULES.md` — for any page, copy, or styling work
- `docs/QUICKSTART.md` — first-run setup (build CLI, init db)
- `.\scripts\bin\harness-cli.exe query matrix` (Windows) or
  `scripts/bin/harness-cli query matrix` (macOS/Linux)

## Working rules

1. **Intake first.** Classify every request and record it before changing code.
2. **Never invent facts.** Missing data becomes a visible
   `[CẦN BỔ SUNG: …]` placeholder and an entry in `docs/product/MISSING.md`
   (`docs/CONTENT_RULES.md`).
3. **Design is decided and justified.** Record the type pair, color system, and
   layout idea with reasons; styling reads tokens; run the design review in
   `docs/DESIGN.md` and cite evidence.
4. **Personal data and the database are high-risk.** Follow
   `docs/decisions/0011-postgresql-database.md`; migrations are reviewed and
   forward-only; secrets never enter git.
5. **The user's brief wins over defaults.** If a brief states its own steps or
   stopping points, follow them; the harness workflow fills the gaps.
6. **No unrequested external APIs.** Social login, email/SMS delivery, maps
   APIs, and payments are separate stories that need an explicit request.
7. **Put code where the architecture says.** Follow `docs/ARCHITECTURE.md`
   (feature-first layout, import rules, data flow). Pages never call the
   database; business rules live in `domain/`. A new top-level folder needs a
   decision record (`docs/decisions/0012-project-structure.md`).
8. **Report honestly.** State what was verified with which command, and what was
   not attempted.

## Operational tool

Use the Rust Harness CLI at `scripts\bin\harness-cli.exe` (Windows) /
`scripts/bin/harness-cli` (macOS/Linux) for intake, stories, traces, and proof
status. If it is missing, build it per `docs/QUICKSTART.md`.

## Tools

External tooling (lint, typecheck, tests, performance checks) is invoked
directly via the `pnpm` scripts in `docs/HARNESS.md` → Validation Ladder. The
optional `harness-cli tool` registry is **not maintained here** — the agent
runtime (e.g. Codex plugins) already provides tool access.
`docs/TOOL_REGISTRY.md` is kept only as reference for the CLI's capabilities.
