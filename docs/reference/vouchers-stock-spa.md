# vouchers-stock SPA reference

**Project:** `vouchers-stock/` (Angular)  
**Compose service:** `vouchers-stock-web`  
**Image:** `mitf-vouchers-stock-web`  
**Local URL:** http://localhost:4200  
**Backend:** Management API only (no local database)

Angular admin SPA for stock management, reports, third-party stock, import/export, and user administration.

---

## Configuration (runtime)

| File | Path | Purpose |
|------|------|---------|
| Runtime config | `public/assets/app-config.json` | **`baseURL`** → Management API (default `http://localhost:5189`) |
| Docker override | `docker/app-config.docker.json` | Mounted into nginx container at runtime |
| Angular env | `src/environments/environment.ts` | Build-time `baseUrl` (legacy) |
| Dev env | `src/environments/environment.development.ts` | Local dev overrides |

**QA compose:** `STOCK_WEB_BASE_URL` injects `baseURL` at container start.

**Local hot reload:** Stop `vouchers-stock-web` container, run `npm start` in `vouchers-stock/` → http://127.0.0.1:4200

---

## Database / Swagger

| Item | Value |
|------|-------|
| Database | **None** — all data via Management API |
| OpenAPI | Use Management API `/swagger` at configured `baseURL` |

---

## SPA routes → API areas

Browser routes are **front-end paths**. API calls use `/api/...` on the Management host.

| SPA route | Function | Typical API prefix |
|-----------|----------|-------------------|
| `dashboard` | Home | — |
| `providers`, `create-provider`, `update-provider` | Provider CRUD | `/api/providers` |
| `main-stock` | Central inventory | `/api/stock/main/*` |
| `foreign-stock`, `configure-foreign-stock` | Remote bank stocks | `/api/Configuration/*`, `/api/stock/foreign/*` |
| `import-vouchers`, `import-request`, `imported-vouchers` | Imports | `/api/import*` |
| `export-vouchers`, `export-request` | Exports to banks | export + gRPC |
| `import-bundles`, `export-bundles`, `exported-bundles` | Bundle flows | `/api/bundle-*` |
| `third-party-stock`, `stock-template`, `third-party-order` | Third-party | `/api/stock/thirdparty/*`, `/api/stock/templates/*` |
| `international-stock` | International | `/api/stock/international/*` |
| `order-lookup`, `card-balance`, `cards-loaded`, `stock-transactions` | Reports | `/api/reports/*` — see [reports-api-guide.md](reports-api-guide.md) |
| `low-stock-rules`, `low-stock-alerts` | Alerts | low-stock endpoints |
| `users` | Admin users | `/api/users/*` |

**Auth:** JWT from `POST /api/authentication/authenticate` stored by SPA; sent as `Authorization: Bearer`.

**E2E tests:** `vouchers-stock/e2e/` — Playwright with `@smoke`, `@journey`, `@internal` tags.
