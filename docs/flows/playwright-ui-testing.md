# Playwright UI testing — vouchers-stock SPA

See also the [UI testing overview](ui-testing-overview.md) for the full Newman → Playwright → Maestro pyramid and runner scripts.

Critical **CRUD and mutation** journeys for the Angular stock management SPA using [Playwright](https://playwright.dev/).

Maestro remains the fast **read-only smoke** layer; Newman covers **API business rules**. Together they form a tiered test pyramid:

```
Newman (API)     → contracts, auth, business rules
Playwright (UI)  → login, users/providers CRUD, stock rules, import/export flows, permissions
Maestro (UI)     → route smoke, filters, read-only navigation
```

## Prerequisites

- Node.js 18+ and npm
- Docker stack with `Mitf.Voucher.Stock.Api` on `http://localhost:5189`
- Angular dev server on `http://127.0.0.1:4200` (started automatically by the runner)

## Quick start

```powershell
# Full Playwright CRUD suite (starts stack + dev server when needed)
pwsh ./scripts/run-playwright-tests.ps1 -SkipDockerStart

# Domain slice
pwsh ./scripts/run-playwright-tests.ps1 -SkipDockerStart -Test "users"

# Visible browser with slow motion (easier to follow)
pwsh ./scripts/run-playwright-tests.ps1 -SkipDockerStart -SlowMo 400

# Headless (CI / faster local runs)
pwsh ./scripts/run-playwright-tests.ps1 -SkipDockerStart -Headless

# Interactive UI — step through tests, watch browser, time-travel debug
pwsh ./scripts/run-playwright-tests.ps1 -SkipDockerStart -Ui

# From vouchers-stock (stack must already be up)
cd vouchers-stock
$env:E2E_BASE_URL = "http://127.0.0.1:4200"
$env:E2E_API_BASE_URL = "http://localhost:5189"
npm run e2e
```

Credentials (Development EasyPass): `admin` / `DevPass123!`

## Watch tests in a visible browser

Playwright runs **headed by default** via `run-playwright-tests.ps1` and `npm run e2e`.

| Mode | Command |
|------|---------|
| Headed + slow motion | `pwsh ./scripts/run-playwright-tests.ps1 -SkipDockerStart -SlowMo 400` |
| Headless | `pwsh ./scripts/run-playwright-tests.ps1 -SkipDockerStart -Headless` or `npm run e2e:headless` |
| Watch mode (headed + slow-mo) | `npm run e2e:watch` (from `vouchers-stock`) |
| Interactive UI | `pwsh ./scripts/run-playwright-tests.ps1 -SkipDockerStart -Ui` or `npm run e2e:ui` |

`-SlowMo 400` adds a 400ms pause between actions so steps are easier to follow. `--ui` opens Playwright’s inspector: pick a test, watch it run, pause, and replay steps.

## Test layout

See **`vouchers-stock/e2e/README.md`** for the full folder map. Structure:

```
vouchers-stock/e2e/
├── fixtures/authenticated.fixture.ts
├── helpers/          auth, api, dialogs, navigation, routes, test-data
├── pages/            page objects per feature area
└── tests/            specs grouped by domain (dashboard, users, providers, stock, import-export)
```

Specs cover: dashboard/main-stock, providers (API seed + UI update), users CRUD/filters/permissions, RBAC matrix (finance/minimal/reclaim), foreign-stock, low-stock, international stock, import/export/bundles/third-party/reports/logs, auth (guest, session, logout, lifecycle, expiry), admin route-health smoke, edge cases, axe a11y, dashboard visual baseline, and the `@journey` management→Jumhoria flow (`journeys` project). Run `npx playwright test --list` for the current count.

Playwright uses **project dependencies** (`e2e/auth.setup.ts`) instead of legacy `globalSetup` — setup traces appear in the HTML report ([Playwright auth docs](https://playwright.dev/docs/auth)).

### Tags and CI tiers

| Tag | When it runs |
|-----|----------------|
| `@smoke` | PR CI (`--grep @smoke`) — includes route-health core slice + logout |
| `@journey` | `npm run e2e:journey` / `journeys` project (serial, long) |
| `@slow` | Nightly / manual full run only |
| `@readonly` | Parallel `readonly` project |
| `@edge` / `@rbac` / `@primo` | Full suite or `-Grep` slice |

```powershell
npm run e2e:smoke
npm run e2e:fast          # skips @slow, @visual, @a11y
pwsh ./scripts/run-playwright-tests.ps1 -SkipDockerStart -Grep "@rbac"
```

| Path | Purpose |
|------|---------|
| `vouchers-stock/e2e/helpers/auth.ts` | Login + force English locale |
| `vouchers-stock/e2e/helpers/dialogs.ts` | Native confirm/prompt handlers |
| `vouchers-stock/e2e/helpers/api.ts` | API seed helpers (users, providers, cleanup) |
| `vouchers-stock/e2e/helpers/api-response.ts` | `CustomResult` response assertions |
| `vouchers-stock/e2e/helpers/downloads.ts` | CSV/export download assertions |
| `vouchers-stock/e2e/auth.setup.ts` | Setup project — admin + RBAC storage states |
| `vouchers-stock/playwright.config.ts` | Projects, tags, retries, parallel readonly |

## Selectors

Prefer `data-testid` on interactive controls:

- `login-submit`, `providers-create-submit`, `providers-edit-submit`
- `users-create-submit`, `users-edit-save-changes`, `users-edit-form-ready`
- `report-order-lookup-export-csv`, `report-order-lookup-search`
- `confirm-dialog-confirm`, `low-stock-rules-create-submit`, `import-vouchers-dry-run`, `import-vouchers-submit`
- `export-vouchers-preview`, `export-vouchers-submit`, `users-edit-save-permissions`, `import-requests-edit-order-id`

Form fields use stable `id` attributes from `app-input` (`#providerId`, `#username`, etc.).

## Artifacts

- HTML report: `artifacts/playwright/report/`
- JUnit: `artifacts/playwright/junit.xml`
- Traces/screenshots on failure: `artifacts/playwright/test-results/`

## Combined backend + UI

```powershell
# Newman → Playwright CRUD → Maestro smoke
pwsh ./scripts/run-all-tests.ps1 -SkipDockerStart -RunFeatureUiFlows

# Skip Playwright when iterating Maestro only
pwsh ./scripts/run-all-tests.ps1 -SkipDockerStart -SkipPlaywright -RunFeatureUiFlows
```

See also [maestro-ui-testing.md](./maestro-ui-testing.md) for the smoke layer.
