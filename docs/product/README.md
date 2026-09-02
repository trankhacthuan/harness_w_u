# Product Docs

`PRODUCT.md` is the canonical product brief. It describes what this repo builds
and is the file the separate UI/UX repository reads to choose a visual style
(see `docs/decisions/0008-ui-ux-boundary.md`). Keep it current.

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
