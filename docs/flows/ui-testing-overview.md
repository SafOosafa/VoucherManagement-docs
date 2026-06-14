# UI and API testing — voucher provider workspace

Tiered verification for the stock management stack:

| Layer | Tool | Scope |
|-------|------|--------|
| API | Newman / Postman | Contracts, auth, business rules |
| UI (CRUD) | [Playwright](playwright-ui-testing.md) | Login, users/providers, stock rules, import/export, permissions |
| UI (smoke) | [Maestro](maestro-ui-testing.md) | Route smoke, filters, read-only navigation |

## One command — full pyramid

```powershell
pwsh ./scripts/run-all-tests.ps1 -SkipDockerStart
```

Options:

- `-SkipPlaywright` — Newman + Maestro only
- `-PlaywrightTest users` — filter Playwright by domain folder/name
- `-RunAllUiFlows` / `-RunFeatureUiFlows` — Maestro breadth

Individual runners:

```powershell
pwsh ./scripts/run-tests.ps1 -SkipDockerStart          # Newman API
pwsh ./scripts/run-playwright-tests.ps1 -SkipDockerStart  # Playwright (headed default)
pwsh ./scripts/run-ui-tests.ps1 -SkipDockerStart       # Maestro smoke
```

## Local prerequisites

- Docker compose stack with `Mitf.Voucher.Stock.Api` on `http://localhost:5189`
- For Playwright during SPA development: stop `vouchers-stock-web` and run `npm start` in `vouchers-stock` (hot reload on `http://127.0.0.1:4200`)
- After Management API seed or compose env changes: `docker compose up -d --force-recreate mitf.voucher.stock.api`

## Artifacts

Test output is written under `artifacts/` (logs, Maestro debug, Playwright reports). These paths are gitignored; only `.gitkeep` placeholders are tracked.

## CI (GitHub Actions)

Workflow: `.github/workflows/qa-regression.yml`

| Trigger | Newman | Playwright |
|---------|--------|------------|
| PR → `dev` | Full collection | Smoke: `auth`, `dashboard`, `import-flow` |
| Push → `dev` | Full collection | Full suite (~54 tests; third-party skipped unless enabled) |
| Nightly (02:00 UTC) | Full collection | Full suite |
| Manual dispatch | Full collection | Full suite; optional `primo_e2e` for third-party specs |

Stack startup uses `scripts/start-qa-stack.ps1` (Postgres, six internal bank APIs, Management API, optional SPA container). Playwright runs headless against the compose web image when `CI=true`.

To enable live Primo third-party UI tests in CI, run workflow dispatch with **primo_e2e** or set `PRIMO_E2E_ENABLED=1` and configure Primo credentials on the Management API.

## Further reading

- [Playwright UI testing](playwright-ui-testing.md) — suite layout, env vars, headed/headless
- [Maestro UI testing](maestro-ui-testing.md) — smoke flows and debug artifacts
- `vouchers-stock/e2e/README.md` — page objects, fixtures, adding tests
