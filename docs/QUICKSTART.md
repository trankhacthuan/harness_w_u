# Quickstart

First-run setup for `harness_w_u`. Windows / PowerShell is the primary path;
bash equivalents follow.

## 1. Build the Harness CLI

The binary is git-ignored, so every clone builds it once. Needs the Rust
toolchain (`rustup` from https://rustup.rs).

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

`harness.db` is local and git-ignored.

```powershell
.\scripts\bin\harness-cli.exe init
.\scripts\bin\harness-cli.exe query matrix
```

## 3. Every task: the loop

1. Classify with `docs/FEATURE_INTAKE.md`, record it:
   `.\scripts\bin\harness-cli.exe intake --type <type> --summary "<text>" --lane <tiny|normal|high-risk>`
2. Read the lane-appropriate context (`docs/CONTEXT_RULES.md`).
3. For product/UI work, read `docs/product/PRODUCT.md` first.
4. Do the work inside the chosen lane only.
5. Run the required checks for the lane (see below).
6. Record a trace: `.\scripts\bin\harness-cli.exe trace --summary "<text>" --outcome <outcome>`
7. Friction? Fix it or `.\scripts\bin\harness-cli.exe backlog add --title "<x>" --pain "<y>"`.

## 4. Validation commands

These exist only after the first story scaffolds the Next.js app
(`docs/decisions/0009-web-stack.md`). Do not claim they pass before then.

| Lane | Run |
| --- | --- |
| tiny | `pnpm lint` + `pnpm typecheck` |
| normal | `pnpm lint && pnpm typecheck && pnpm test` (+ `pnpm test:e2e` if a flow changed) |
| high-risk | `pnpm lint && pnpm typecheck && pnpm test && pnpm test:e2e && pnpm build` |

## 5. Scaffolding the app (first implementation story only)

```powershell
pnpm create next-app@latest . --ts --app --eslint --use-pnpm
```

Then wire `lint`, `typecheck` (`tsc --noEmit`), `test` (Vitest), `test:e2e`
(Playwright + `@axe-core/playwright`) in `package.json`, and record the setup in
the story's evidence. Consume the UI/UX repo's token / class convention — do not
create a new one here.

## 6. Working with the UI/UX repo

- Keep `docs/product/PRODUCT.md` current; it is what the UI/UX repo designs against.
- Deliver functional code and `PRODUCT.md` to that repo manually (via Codex).
- Consume styling back as tokens / class conventions / component specs without
  re-deriving them (`docs/decisions/0008-ui-ux-boundary.md`).
