# ci-doctor-action

Audit your GitHub Actions workflows for **waste, cost, and security gaps** on every pull request.

Wraps the [`ci-doctor`](https://www.npmjs.com/package/ci-doctor) CLI (16 rules, 46 tests) and:

- posts a markdown summary as a sticky **PR comment**
- uploads SARIF to **GitHub Code Scanning** (Security tab)
- fails the build only on the severity threshold you choose

MIT, no telemetry, no auth required.

## Quick start

`.github/workflows/ci-doctor.yml`:

```yaml
name: ci-doctor
on:
  pull_request:
    paths:
      - '.github/workflows/**'
permissions:
  contents: read
  pull-requests: write
  security-events: write
jobs:
  audit:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: depmedicdev-byte/ci-doctor-action@v1
```

That's it. Open a PR that touches `.github/workflows/`, you'll get a comment like:

| severity | rule | location | message |
| --- | --- | --- | --- |
| warn | docker-no-pin | release.yml | container image `node:22` is not pinned to @sha256 |
| warn | service-no-healthcheck | test.yml | postgres service has no `--health-cmd` |
| warn | expensive-runner | build.yml | uses `windows-latest` for `npm test` |

## Inputs

| input | default | description |
| --- | --- | --- |
| `path` | `.` | Path to scan (auto-discovers `.github/workflows`) |
| `fail-on` | `error` | Fail the job on `info`, `warn`, or `error` |
| `upload-sarif` | `true` | Upload to Code Scanning (needs `security-events: write`) |
| `comment-on-pr` | `true` | Sticky PR comment (needs `pull-requests: write`) |
| `ci-doctor-version` | `latest` | Pin to a specific npm version |
| `only` | _empty_ | Comma-separated rule IDs to run exclusively |
| `disable` | _empty_ | Comma-separated rule IDs to skip |

## Outputs

- `sarif-path` — `ci-doctor.sarif`
- `markdown-path` — `ci-doctor.md`
- `finding-count` — total findings (integer)

## Recipes

### Block PRs only on errors, warn on the rest

```yaml
- uses: depmedicdev-byte/ci-doctor-action@v1
  with:
    fail-on: error
```

### Hard mode: block any warning

```yaml
- uses: depmedicdev-byte/ci-doctor-action@v1
  with:
    fail-on: warn
```

### Disable a noisy rule

```yaml
- uses: depmedicdev-byte/ci-doctor-action@v1
  with:
    disable: missing-concurrency,no-pin-actions
```

### Run only the security rules

```yaml
- uses: depmedicdev-byte/ci-doctor-action@v1
  with:
    only: docker-no-pin,no-pin-actions,after-script-leaks
```

### Pin to a specific ci-doctor version (recommended)

```yaml
- uses: depmedicdev-byte/ci-doctor-action@v1
  with:
    ci-doctor-version: '0.5.0'
```

## What it checks (16 rules)

**security**: `no-pin-actions`, `pull-request-target-checkout`, `script-injection-context`, `insecure-checkout-token`, `docker-no-pin`, `after-script-leaks`

**cost**: `expensive-runner`, `missing-concurrency`, `missing-cache`, `wide-paths`, `cron-storm`, `service-no-healthcheck`

**reliability**: `flaky-retries`, `missing-timeout-minutes`, `legacy-actions-version`

**hygiene**: `actions-floating-tag`

Full descriptions: <https://depmedicdev-byte.github.io/rules.html>

## Related

- CLI: <https://www.npmjs.com/package/ci-doctor>
- VS Code extension: `depmedic-vscode` (inline squigglies for the same 16 rules)
- GitHub App: `depmedic-bot` (zero-config, Marketplace listing pending)
- GitLab port: <https://www.npmjs.com/package/gitlab-ci-doctor>
- Bitbucket port: <https://www.npmjs.com/package/bitbucket-ci-doctor>
- Pin all `uses:` to SHAs: `npx pin-actions`

## License

MIT © depmedic
