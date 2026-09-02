# Agent Instructions

## What this repo is

`harness_w_u` is a repository harness for building **websites**. It owns product
definition, functional implementation, and validation. Visual UI/UX design is
owned by a **separate repository** — do not build a design system, palette, or
component styling here (`docs/decisions/0008-ui-ux-boundary.md`).

Stack: Next.js (App Router) + TypeScript + pnpm (`docs/decisions/0009-web-stack.md`).
The app is not scaffolded until the first implementation story needs it.

## Before work, read

- `README.md`
- `docs/HARNESS.md`
- `docs/FEATURE_INTAKE.md`
- `docs/ARCHITECTURE.md`
- `docs/CONTEXT_RULES.md`
- `docs/product/PRODUCT.md` — the product brief
- `docs/QUICKSTART.md` — first-run setup (build CLI, init db)
- `.\scripts\bin\harness-cli.exe query matrix` (Windows) or
  `scripts/bin/harness-cli query matrix` (macOS/Linux)

## Operational tool

Use the Rust Harness CLI at `scripts\bin\harness-cli.exe` (Windows) /
`scripts/bin/harness-cli` (macOS/Linux) for intake, stories, traces, and proof
status. If it is missing, build it per `docs/QUICKSTART.md`.

## Tools

External tooling (lint, typecheck, tests, deploy checks) is invoked directly via
the `pnpm` scripts in `docs/HARNESS.md` → Validation Ladder. The optional
`harness-cli tool` registry is **not maintained here** — the agent runtime
(e.g. Codex plugins) already provides tool access. `docs/TOOL_REGISTRY.md` is
kept only as reference for the CLI's capabilities.
