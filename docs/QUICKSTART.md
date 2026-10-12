# Quickstart

First-run setup for `harness_w_u`. Windows / PowerShell is the primary path;
bash equivalents follow.

## 0. Prerequisites

| Tool | Needed for | Check |
| --- | --- | --- |
| Git | version control | `git --version` |
| Rust toolchain (`rustup`) | building the Harness CLI | `cargo --version` |
| Node.js LTS + pnpm | the web app and its checks (once scaffolded) | `node --version`, `pnpm --version` |
| PostgreSQL 16+ (Docker, native, or hosted) | database stories and integration tests | `psql --version` |

Node.js and pnpm are not required to use the Harness CLI itself or to edit docs;
they are required before the first implementation story. PostgreSQL is required
only for the features that use the database (for example reservations) and for
`test:integration`: the rest of the site builds and runs without it.

## 1. Build the Harness CLI

The binary is git-ignored, so every clone builds it once. (The one-line
installer in `README.md` downloads a prebuilt binary instead.)

PowerShell:

```powershell
cargo build --release -p harness-cli
New-Item -ItemType Directory -Force scripts\bin | Out-Null
Copy-Item target\release\harness-cli.exe scripts\bin\harness-cli.exe -Force
.\scripts\bin\harness-cli.exe --version
```

bash:

```bash
cargo build --release -p harness-cli
mkdir -p scripts/bin
cp target/release/harness-cli scripts/bin/harness-cli
scripts/bin/harness-cli --version
```

## 2. Initialize the durable database

`harness.db` is the harness's own SQLite record store. It is local and
git-ignored, and unrelated to the product's PostgreSQL database.

```powershell
.\scripts\bin\harness-cli.exe init
.\scripts\bin\harness-cli.exe query matrix
```

## 3. Every task: the loop

1. Classify with `docs/FEATURE_INTAKE.md`, record it:
   `.\scripts\bin\harness-cli.exe intake --type <type> --summary "<text>" --lane <tiny|normal|high-risk>`
2. Read the lane-appropriate context (`docs/CONTEXT_RULES.md`).
3. For product, design, or content work, read `docs/product/PRODUCT.md`,
   `docs/DESIGN.md`, and `docs/CONTENT_RULES.md` first.
4. Do the work inside the chosen lane only.
5. Run the required checks for the lane (see below).
6. Record a trace: `.\scripts\bin\harness-cli.exe trace --summary "<text>" --outcome <outcome>`
7. Friction? Fix it or `.\scripts\bin\harness-cli.exe backlog add --title "<x>" --pain "<y>"`.

## 4. Validation commands

These exist only after the first story scaffolds the app
(`docs/decisions/0009-web-stack.md`). Do not claim they pass before then.

| Lane | Run |
| --- | --- |
| tiny | `pnpm lint` + `pnpm typecheck` |
| normal | `pnpm lint && pnpm typecheck && pnpm test` (+ `pnpm test:e2e` if a flow changed; + design review if appearance changed) |
| high-risk | the full `test:release` ladder in `docs/HARNESS.md` + design review |

## 5. Scaffolding the app (first implementation story only)

```powershell
pnpm create next-app@latest . --ts --app --eslint --use-pnpm
```

Then wire `lint`, `typecheck` (`tsc --noEmit`), `test` (Vitest), `test:e2e`
(Playwright + `@axe-core/playwright`), `test:perf` (Lighthouse), and the `db:*`
scripts in `package.json`; create `app/styles/tokens.css`; record the setup in
the story's evidence.

## 6. Starting a restaurant site

1. Fill `docs/templates/brief-restaurant.md` and mirror the facts into
   `docs/product/PRODUCT.md`.
2. Give the brief to the agent. Placeholders (`[CẦN BỔ SUNG: …]`) are tracked in
   `docs/product/MISSING.md`.
3. The reservation form and its database are a high-risk story; expect a story
   folder and a decision record before code.
