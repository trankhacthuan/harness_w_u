# Changelog

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
