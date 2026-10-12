# Product Docs

`PRODUCT.md` is the canonical product brief. It describes what this repo builds
and is the starting point for design (`docs/DESIGN.md`) and implementation. Keep
it current. `MISSING.md` is the ledger of content and assets still needed from
the owner (`docs/CONTENT_RULES.md`).

When a spec grows past a single brief, derive smaller product contract files
here and name them by the product domains that actually exist, for example
`overview.md`, `catalog.md`, `checkout.md`, `accounts.md`, or
`content-model.md`. `PRODUCT.md` stays the top-level summary that links to them.

Do not create domain files before they are needed just to fill the folder.
Empty structure is healthier than fake product truth.

## Update Rule

When behavior changes:

1. Update the affected product doc.
2. Update or create the story packet.
3. Update durable proof status with `scripts/bin/harness-cli story add` or
   `scripts/bin/harness-cli story update` (`.exe` on Windows).
4. Record a decision if the change affects architecture, scope, risk, or a
   previously settled product rule.
