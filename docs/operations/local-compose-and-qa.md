# Local compose and QA environment

Authoritative guide for running the stack locally, configuring QA hosts, CORS, and automated test prerequisites.

---

## Starting the stack

```powershell
# Full local build
docker compose up --build

# QA-oriented (Hub images)
docker compose -f docker-compose.qa.yml up -d

# CI-style (from repo root, as GHA does)
docker compose -f docker-compose.yml -f docker-compose.ci.yml up -d --build
```

**QA helper:**

```powershell
pwsh ./scripts/start-qa-stack.ps1 -IncludeWeb
```

Starts postgres, six internal banks, management API, optionally web; waits on `GET /health/ready`.

---

## CORS (Management API)

Browser requests from the SPA send an exact origin. Configure in appsettings or compose:

| Env var (QA) | Binds to |
|--------------|----------|
| `STOCK_CORS_ORIGIN_0` | `OriginsOption__AllowedOrigins__0` |
| `STOCK_CORS_ORIGIN_1` | `OriginsOption__AllowedOrigins__1` |

Example origins:

- Containerized SPA: `http://localhost:4200`
- Local npm dev: `http://127.0.0.1:4200`

**Symptom:** Swagger/Postman works but browser shows CORS error → missing or wrong origin entry.

---

## SPA base URL

| Env var | Purpose |
|---------|---------|
| `STOCK_WEB_BASE_URL` | Written into `app-config.json` `baseURL` at container start |
| `STOCK_API_HOST_PORT` | Host port for Management API (default 5189) |
| `STOCK_WEB_HOST_PORT` | Host port for SPA (default 4200) |

For local Angular dev with hot reload:

1. `docker compose up` (without web or stop `vouchers-stock-web`)
2. `cd vouchers-stock && npm start`
3. Ensure CORS includes `http://127.0.0.1:4200`

---

## Management seed flags

| Variable | Effect |
|----------|--------|
| `ManagementDataSeed__Enabled=true` | Dev login `admin` / `DevPass123!` |
| `ManagementDataSeed__IncludeThirdPartyProviders` | Primo Wallet config |
| `ManagementDataSeed__IncludeDockerBankForeignStocks` | Wire six banks + gRPC |
| `ManagementDataSeed__IncludeDockerDemoExportsToInternalBanks` | Demo exports (off in CI) |

`GET /health/ready` returns **503** until seed completes.

---

## Third-party provider env binding

Nested ASP.NET binding requires **double underscores** between section and property:

```
ThirdPartyProviders__0__Config__Username
```

Not `ThirdPartyProviders__0__ConfigUsername` — extra/missing underscores break binding.

Verify inside container (Linux QA):

```bash
printenv | grep OriginsOption
printenv | grep ThirdPartyProviders
```

---

## Recreate vs restart

After `.env` or compose env changes for Management API:

```powershell
docker compose up -d --force-recreate mitf.voucher.stock.api
```

`docker compose restart` alone often leaves stale environment in the running container.

---

## Newman cold start

On a cold seeded stack, some requests (e.g. Card Balance Summary) may need **~120s** timeout:

```powershell
pwsh ./scripts/run-tests.ps1 -SkipDockerStart -RequestTimeoutMs 120000
```

---

## Playwright tags

| Tag | Scope |
|-----|-------|
| `@smoke` | Default CI smoke |
| `@journey` | Full import→export→bundles→international |
| `@internal` | Internal API harness (Jumhoria :5238) |
| `@slow`, `@visual`, `@a11y` | Excluded from default CI grep |

Use `127.0.0.1` not `localhost` for Cursor browser automation on Windows.

---

## Pulling published images

Set in `.env` or shell:

```
DOCKER_REGISTRY=docker.io/anstwechy/
```

Image names use **`mitf-`** prefix per GitHub Actions (`docker-hub.yml`).

---

## Related

- [Production deployment](production-deployment.md) — full port matrix and CI
- [UI testing overview](../flows/ui-testing-overview.md) — test pyramid
- [Management API reference](../reference/management-api.md) — config tables
