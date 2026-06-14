# UI and API testing — voucher provider workspace

Tiered verification for the stock management stack:

| Layer | Tool | Scope |
|-------|------|--------|
| API | Newman / Postman | Contracts, auth, business rules |
| UI (CRUD) | Playwright | Login, users/providers, stock rules, import/export, permissions |
| UI (smoke) | Maestro | Route smoke, filters, read-only navigation |

---

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

---

## Local prerequisites

- Docker compose stack with `Mitf.Voucher.Stock.Api` on `http://localhost:5189`
- For Playwright during SPA development: stop `vouchers-stock-web` and run `npm start` in `vouchers-stock` (hot reload on `http://127.0.0.1:4200`)
- After Management API seed or compose env changes: `docker compose up -d --force-recreate mitf.voucher.stock.api`

---

## CI (GitHub Actions)

Workflow: `.github/workflows/qa-regression.yml`

| Trigger | Newman | Playwright |
|---------|--------|------------|
| PR → `dev` | Full collection | Smoke: `auth`, `dashboard`, `import-flow` |
| Push → `dev` | Full collection | Full suite (~54 tests; third-party skipped unless enabled) |
| Nightly (02:00 UTC) | Full collection | Full suite |
| Manual dispatch | Full collection | Full suite; optional `primo_e2e` |

Stack startup uses `scripts/start-qa-stack.ps1` (Postgres, six internal bank APIs, Management API, optional SPA container).

---

## Further reading

- [Playwright UI testing](playwright-ui-testing.md) — suite layout, env vars, headed/headless
- [Maestro UI testing](maestro-ui-testing.md) — smoke flows and debug artifacts
- [UAT checklist](uat-checklist-voucher-stock.md) — manual audit checklist
