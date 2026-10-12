# Changelog

## 2026-10-12 - Single-repo site builder

- One repository now owns brief, visual design, front end, back end, database,
  and validation. `0010-single-repo-design-ownership.md` supersedes
  `0008-ui-ux-boundary.md`; `UX_UI_HARNESS` becomes an optional reference.
- Stack amended (`0009`): CSS Modules + CSS custom properties, no Tailwind;
  motion via CSS + `IntersectionObserver`.
- New `0011-postgresql-database.md`: PostgreSQL, Drizzle ORM, forward-only SQL
  migrations, personal-data rules.
- New docs: `docs/DESIGN.md` (art direction, tokens, motion, performance budget,
  design review), `docs/CONTENT_RULES.md` (no invented facts, placeholders),
  `docs/product/MISSING.md` (missing-content ledger),
  `docs/templates/brief-restaurant.md` (restaurant site brief).
- Validation ladder extended with integration (real PostgreSQL), performance
  (Lighthouse budget), and a design review gate.
- The site must not depend on PostgreSQL to start: lazy `DATABASE_URL`
  validation, no database access at build time, `503` + call-button fallback,
  and "not verified" as an explicit status until real database tests run.
- Tests use a separate `TEST_DATABASE_URL` and abort if it is unset or equals
  `DATABASE_URL`; every `.env*` file is git-ignored except `.env.example`.
- Installer: one-line install works from any folder (falls back to
  `~/harness_w_u`), runs `harness-cli init`, and now ships the new docs and the
  previously missing `QUICKSTART.md`, `PRODUCT.md`, and decisions 0008–0011.

## 2026-09-01 - Fork baseline

- Forked from `hoangnb24/repository-harness` at Harness CLI `v0.1.10`
  (upstream merge `e3a83390be59eafcf361afe61672db1a9ed0a440`).
- Re-owned as `trankhacthuan/harness_w_u`, a private repository harness for
  building websites. Visual UI/UX design lives in a separate repository; this
  repo owns product definition, functional implementation, and validation.
- Git history reset to a single initial commit. Upstream history is preserved
  locally as `harness_w_u-original-history.bundle` outside the repository.
- Ownership metadata updated: `LICENSE`, `Cargo.toml`, `README.md`, installer
  scripts.
- Tool Registry demoted to optional (agent runtime, e.g. Codex plugins,
  provides tool access).
- Added web stack decision, product brief, and concrete validation ladder.

<!-- New entries are appended above this line by the Post-Merge Maintenance workflow. -->
