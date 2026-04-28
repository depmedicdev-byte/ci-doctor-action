# Changelog

## v1.0.0 — 2026-04-28

Initial release. Composite Action wrapping `ci-doctor@^0.5.0`.

- 16 audit rules (security, cost, reliability, hygiene)
- Sticky PR comment (one comment per PR, updates in place)
- SARIF upload to GitHub Code Scanning
- `fail-on`, `only`, `disable`, `ci-doctor-version` inputs
- Outputs: `sarif-path`, `markdown-path`, `finding-count`
- Pinned dependencies (`upload-sarif@v3.27.0`, `github-script@v7.0.1`) by SHA
