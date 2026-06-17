# Maestro UI testing — vouchers-stock SPA

Fast **read-only smoke** regression for the Angular stock management SPA using [Maestro](https://maestro.mobile.dev/) (web beta).

**CRUD and mutations** belong in Playwright — see [playwright-ui-testing.md](./playwright-ui-testing.md).

## Test pyramid

| Layer | Tool | Scope |
|-------|------|--------|
| API | Newman | Auth, contracts, business rules |
| UI CRUD | Playwright | Provider/user CRUD, low-stock rules, import dry-run |
| UI smoke | Maestro | Routes, filters, read-only navigation |

## Prerequisites

- Java 17+ and Maestro CLI 2.x (`maestro --version`)
- Docker stack with `Mitf.Voucher.Stock.Api` on `http://localhost:5189`
- Angular dev server on `http://127.0.0.1:4200` (preferred for hot reload)

## Quick start

```powershell
# Route smoke (single session)
pwsh ./scripts/run-ui-tests.ps1 -SkipDockerStart

# One feature flow (recommended — fresh login, ~2–4 min each)
pwsh ./scripts/run-ui-tests.ps1 -SkipDockerStart -FeatureFlow features/02-providers-and-main-stock.yaml

# All feature flows, each in its own session (default; avoids session timeout)
pwsh ./scripts/run-ui-tests.ps1 -SkipDockerStart -RunFeatureFlows

# All features + route smoke
pwsh ./scripts/run-ui-tests.ps1 -SkipDockerStart -RunAllFlows
```

Credentials (Development EasyPass): `admin` / `admin`

Login subflow forces English via `localStorage.setItem('vouchers-stock.locale', 'en')`.

## Flow layout

| Path | Purpose |
|------|---------|
| `maestro/flows/regression.yaml` | Fast route smoke — every primary menu URL |
| `maestro/flows/features/01-*.yaml` … `11-*.yaml` | Read-only per-feature smoke |
| `maestro/flows/subflows/bootstrap-session.yaml` | Login + dismiss onboarding |

Prepared flows live under `artifacts/maestro/flows-prepared/` (env substitution for `${WEB_BASE_URL}`, etc.).

## Feature smoke coverage

| Flow | Coverage |
|------|----------|
| `01-dashboard` | Provider + stock filters |
| `02-providers-and-main-stock` | Providers list + main stock filter |
| `03-foreign-stock-and-low-stock` | Foreign stock, rules page, alert filters |
| `04-import-export-vouchers` | Import/export form fill + export preview |
| `05-import-export-requests` | Tracking lists |
| `06-bundles` | Form fill + cancel (no submit) |
| `07-reports-core` | Search only |
| `08-reports-analytics` | Presets only |
| `09-users-and-logs` | Users filter + activity log |
| `10-third-party` | Third-party pages + provider filters |
| `11-international-stock` | International stock list |

## Selector notes

- Prefer stable `id` attributes (`providerId`, `stockId`, `username`).
- Report pages: select `#stockId` + **30 days** preset instead of scrolling to **Search** buttons.

## Artifacts

- JUnit: `artifacts/maestro/maestro-junit-<flow>-*.xml`
- Debug: `artifacts/maestro/debug-<flow>-*`

## Combined backend + UI

```powershell
# Newman → Playwright CRUD → Maestro smoke
pwsh ./scripts/run-all-tests.ps1 -SkipDockerStart -RunFeatureUiFlows
```
