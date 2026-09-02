# harness_w_u

A private repository harness for building **websites** with coding agents
(Codex, Claude Code). It gives an agent the project context it needs before it
touches code: what the product is, where to start, how risky a change is, and
what proof "done" requires.

Forked from [`hoangnb24/repository-harness`](https://github.com/hoangnb24/repository-harness)
(MIT) and adapted for one workflow.

## Split of responsibilities

| This repo (`harness_w_u`) | The separate UI/UX repo |
| --- | --- |
| Product definition (`docs/product/PRODUCT.md`) | Visual language, design tokens, component styling |
| Routes, pages, data, state (loading / empty / error) | Look and feel of those states |
| Semantic, accessible, responsive markup | CSS, theme, motion, brand |
| Functional + a11y validation | Visual review |

The interface between them is `docs/product/PRODUCT.md`: the UI/UX repo reads it
(delivered manually via Codex) to choose a style that fits the product. See
`docs/decisions/0008-ui-ux-boundary.md`.

## Stack

Next.js (App Router) + TypeScript + pnpm, tests with Vitest + Playwright
(`docs/decisions/0009-web-stack.md`). The app is scaffolded by the first
implementation story, not before.

## Get it running

Clone the repo (private — uses your GitHub login):

```powershell
git clone https://github.com/trankhacthuan/harness_w_u.git
cd harness_w_u
```

Build the Harness CLI and initialize the durable database (the binary and
`harness.db` are git-ignored, so every clone does this once — full steps in
`docs/QUICKSTART.md`):

```powershell
cargo build --release -p harness-cli
New-Item -ItemType Directory -Force scripts\bin | Out-Null
Copy-Item target\release\harness-cli.exe scripts\bin\harness-cli.exe -Force
.\scripts\bin\harness-cli.exe init
.\scripts\bin\harness-cli.exe query matrix
```

Then open the folder with your agent — Codex reads `AGENTS.md` automatically:

```powershell
codex
```

Needs the Rust toolchain (`rustup` from https://rustup.rs). macOS/Linux: same
steps, drop the `.exe` and use `mkdir -p scripts/bin`.

## How work flows

```text
product idea
  -> docs/product/PRODUCT.md (brief the UI/UX repo designs against)
  -> feature intake: tiny / normal / high-risk   (docs/FEATURE_INTAKE.md)
  -> story packet                                (docs/templates/story.md)
  -> implementation (function + a11y, no bespoke styling)
  -> validation ladder                           (docs/HARNESS.md)
  -> trace + decision captured for the next agent
```

## Layout

```text
AGENTS.md              agent entrypoint (read first)
CLAUDE.md              Claude Code shim that imports AGENTS.md + FEATURE_INTAKE.md
docs/
  HARNESS.md           collaboration model + validation ladder
  FEATURE_INTAKE.md    risk classification
  ARCHITECTURE.md      Next.js architecture + UI/UX boundary rules
  CONTEXT_RULES.md     what to read per lane / phase
  QUICKSTART.md        first-run setup
  product/PRODUCT.md   the product brief
  decisions/           durable decisions (0008 UI/UX boundary, 0009 stack, ...)
  stories/             story packets and backlog
  templates/           story / decision / validation templates
  history/             archived upstream phase docs (reference only)
crates/harness-cli/    Rust CLI: durable layer (SQLite records of intake, stories, traces)
scripts/               installer + CLI build helper
```

## The Harness CLI

`scripts\bin\harness-cli.exe` is the operational tool. It stores intake
classifications, story status, decisions, and execution traces in a local
git-ignored `harness.db`.

```powershell
.\scripts\bin\harness-cli.exe intake --type change-request --summary "..." --lane normal
.\scripts\bin\harness-cli.exe story add --id US-001 --title "..." --lane normal
.\scripts\bin\harness-cli.exe story update --id US-001 --unit 1 --e2e 1
.\scripts\bin\harness-cli.exe trace --summary "..." --outcome success
.\scripts\bin\harness-cli.exe query matrix
```

The optional `harness-cli tool` registry is not maintained here — the agent
runtime already provides tool access.

## Installing this harness into another project

This is **not** how you open this repo — it copies the harness skeleton
(`AGENTS.md`, `docs/`, `scripts/`) into a *different* website project. Run it
from that project's folder.

Remote one-liner — works only while this repo is **public** (the raw URL 404s
for a private repo):

```powershell
& ([scriptblock]::Create((irm "https://raw.githubusercontent.com/trankhacthuan/harness_w_u/main/scripts/install-harness.ps1"))) -Yes
```

```bash
curl -fsSL "https://raw.githubusercontent.com/trankhacthuan/harness_w_u/main/scripts/install-harness.sh" | bash -s -- --yes
```

While the repo is private, clone it once and run the script **locally** (this is
the supported path):

```powershell
git clone https://github.com/trankhacthuan/harness_w_u.git
.\harness_w_u\scripts\install-harness.ps1 -Directory "D:\path\to\website" -Yes
```

```bash
git clone https://github.com/trankhacthuan/harness_w_u.git
harness_w_u/scripts/install-harness.sh --directory /path/to/website --yes
```

Run from a clone, the installer copies the harness files from that clone (no
network) and reuses the `harness-cli` binary you already built in it. No
published CLI release is needed; add `--no-cli` / `-NoCli` to skip the binary
entirely. Conflicts on an existing `AGENTS.md` / `docs/` / `scripts/` are
handled with `--merge` or `--override`.
