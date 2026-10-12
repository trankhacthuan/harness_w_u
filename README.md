# harness_w_u

> A repository harness for building **websites** with coding agents.

`harness_w_u` gives an agent (Codex, Claude Code) the project context it needs
*before* it edits code: what the product is, what to read first, how risky a
change is, and what proof "done" requires. Brief, visual design, front end,
back end, database, and validation all live in this one repository.

Forked from [`hoangnb24/repository-harness`](https://github.com/hoangnb24/repository-harness)
(MIT).

---

## Quick start — one line

Paste one line in any terminal (Codex CLI, VS Code, PowerShell, bash). No clone,
no pre-existing folder, no manual build.

**Windows (PowerShell):**

```powershell
& ([scriptblock]::Create((irm "https://raw.githubusercontent.com/trankhacthuan/harness_w_u/main/scripts/install-harness.ps1"))) -Yes
```

**macOS / Linux / Git Bash:**

```bash
curl -fsSL "https://raw.githubusercontent.com/trankhacthuan/harness_w_u/main/scripts/install-harness.sh?$(date +%s)" | bash -s -- --yes
```

The installer:

1. Installs the harness (`AGENTS.md`, `docs/`, `scripts/`) into the **current
   folder** — or, if the terminal is in your home folder, a system folder (for
   example the Codex app's own install directory), or a read-only location, into
   a new `~/harness_w_u` folder instead.
2. Downloads the `harness-cli` binary from the
   [latest release](https://github.com/trankhacthuan/harness_w_u/releases) and
   verifies its SHA-256.
3. Creates the local `harness.db` (`harness-cli init`).
4. Prints the next step: `cd` into the folder and run `codex` (or open the folder
   in VS Code / Claude Code — the agent reads `AGENTS.md` automatically).

**Choose the folder yourself** — pass a path:

```powershell
& ([scriptblock]::Create((irm "https://raw.githubusercontent.com/trankhacthuan/harness_w_u/main/scripts/install-harness.ps1"))) -Yes -Directory "D:\my-site"
```

```bash
curl -fsSL "https://raw.githubusercontent.com/trankhacthuan/harness_w_u/main/scripts/install-harness.sh" | bash -s -- --yes --directory /path/to/my-site
```

**Other flags** — `--merge` / `--override` (`-Merge` / `-Override`) resolve
conflicts with an existing `AGENTS.md` / `docs/` / `scripts/`; `--claude` adds a
`CLAUDE.md` shim; `--no-cli` / `-NoCli` skips the binary. Full list:
[`scripts/README.md`](scripts/README.md).

---

## Overview

One repository takes a brief to a finished site — no separate design repo and no
manual hand-off (`docs/decisions/0010-single-repo-design-ownership.md`).

| Concern | Where |
| --- | --- |
| Product brief | `docs/product/PRODUCT.md`, `docs/templates/brief-restaurant.md` |
| Art direction, tokens, motion, performance budget | `docs/DESIGN.md` |
| Content integrity (no invented facts) | `docs/CONTENT_RULES.md`, `docs/product/MISSING.md` |
| Front end and back end | Next.js App Router, CSS Modules |
| Database | PostgreSQL via Drizzle (`docs/decisions/0011-postgresql-database.md`) |
| Proof | validation ladder in `docs/HARNESS.md` |

**Stack** — Next.js (App Router) + TypeScript + pnpm, CSS Modules + CSS
variables, PostgreSQL, tested with Vitest, Playwright, and Lighthouse
(`docs/decisions/0009-web-stack.md`). The application is scaffolded by the first
implementation story, not before.

> `UX_UI_HARNESS` (a separate design-system repository) is an optional reference.
> Nothing here depends on it.

---

## Developing this repository

Only needed to work on the harness itself (Rust CLI, installers, docs). To *use*
the harness, run the one-line install above.

**Prerequisites** — Git, the Rust toolchain ([rustup](https://rustup.rs)), and
your agent CLI (e.g. Codex). Node.js + pnpm and PostgreSQL are only needed once
the app is scaffolded (`docs/QUICKSTART.md`).

```powershell
# 1. Clone
git clone https://github.com/trankhacthuan/harness_w_u.git
cd harness_w_u

# 2. Build the Harness CLI (git-ignored; every clone does this once)
cargo build --release -p harness-cli
New-Item -ItemType Directory -Force scripts\bin | Out-Null
Copy-Item target\release\harness-cli.exe scripts\bin\harness-cli.exe -Force

# 3. Initialize the durable database and check status
.\scripts\bin\harness-cli.exe init
.\scripts\bin\harness-cli.exe query matrix

# 4. Open with your agent — it reads AGENTS.md automatically
codex
```

On macOS / Linux, drop the `.exe` and use `mkdir -p scripts/bin`. Full notes:
[`docs/QUICKSTART.md`](docs/QUICKSTART.md).

---

## Workflow

Every request passes through intake before code changes:

```text
product idea
  │
  ├─ docs/product/PRODUCT.md ....... the brief; gaps tracked in MISSING.md
  ├─ feature intake ............... tiny / normal / high-risk   (docs/FEATURE_INTAKE.md)
  ├─ story packet ................. one vertical slice          (docs/templates/story.md)
  ├─ design decisions ............ type · color · layout idea  (docs/DESIGN.md)
  ├─ implementation .............. front end · back end · database
  ├─ validation ladder ........... lint · types · tests · e2e · perf  (docs/HARNESS.md)
  ├─ design review ............... six questions, cited evidence
  └─ trace + decision ............ evidence for the next agent
```

---

## Repository layout

```text
AGENTS.md              Agent entrypoint — read first.
CLAUDE.md              Claude Code shim; imports AGENTS.md + FEATURE_INTAKE.md.
docs/
  HARNESS.md           Collaboration model and validation ladder.
  FEATURE_INTAKE.md    Risk classification and lanes.
  ARCHITECTURE.md      Next.js + PostgreSQL architecture and layering rules.
  DESIGN.md            Art direction, tokens, motion, performance, design review.
  CONTENT_RULES.md     No invented facts; placeholders and the missing ledger.
  CONTEXT_RULES.md     What to read, per lane and phase.
  QUICKSTART.md        First-run setup.
  product/PRODUCT.md   The product brief.
  product/MISSING.md   Content and assets still needed from the owner.
  decisions/           Durable decisions (ADRs).
  stories/             Story packets and backlog.
  templates/           Story, decision, and validation templates.
  history/             Archived upstream planning docs (reference only).
crates/harness-cli/    Rust CLI — the durable layer (SQLite).
scripts/               Installer and CLI build helpers.
```

---

## The Harness CLI

`scripts\bin\harness-cli.exe` records intake classifications, story status,
decisions, and execution traces in a local, git-ignored `harness.db`.

| Command | Purpose |
| --- | --- |
| `harness-cli init` | Create `harness.db`. |
| `harness-cli intake --type <t> --summary "…" --lane <lane>` | Record a classified request. |
| `harness-cli story add --id US-001 --title "…" --lane <lane>` | Open a story. |
| `harness-cli story update --id US-001 --unit 1 --e2e 1` | Record proof status. |
| `harness-cli trace --summary "…" --outcome <outcome>` | Log an execution trace. |
| `harness-cli query matrix` | Show behavior-to-proof status. |

The optional `harness-cli tool` registry is not maintained here — the agent
runtime already provides tool access.

---

## Offline install (from a clone)

Without network access, install from a local clone — files are copied from the
clone and a `harness-cli` binary built there is reused:

```powershell
git clone https://github.com/trankhacthuan/harness_w_u.git
.\harness_w_u\scripts\install-harness.ps1 -Directory "D:\path\to\project" -Yes
```

```bash
git clone https://github.com/trankhacthuan/harness_w_u.git
harness_w_u/scripts/install-harness.sh --directory /path/to/project --yes
```

> The one-line install needs this repository to be **public** (the raw URLs
> return `404` for a private repo) and a published CLI release
> (`harness-cli-v*` tag → *Harness CLI Release* workflow).

---

## Provenance & license

Derived from `hoangnb24/repository-harness` (MIT); see [`LICENSE`](LICENSE).
Changes are re-owned under the same license.
