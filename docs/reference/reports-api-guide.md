# Reports API guide

Maps **SPA browser routes** to **Management API HTTP paths**. The SPA route is what users see in the address bar; QA and integrators must call the `/api/...` URL on the Management host (default http://localhost:5189).

---

## Route mapping

| SPA route | API path | Purpose |
|-----------|----------|---------|
| `/reports/order-lookup` | `GET /api/reports/order-lookup` | Search orders by criteria |
| `/reports/card-balance` | `GET /api/reports/card-balance-summary` | Card balance summary |
| `/reports/cards-loaded` | `GET /api/reports/cards-loaded` | Cards loaded report |
| `/reports/stock-transactions` | `GET /api/stock-transactions` | Stock transaction listing |
| `/reports/stock-transaction-statistics` | `GET /api/exported-voucher-statistics` | Transaction statistics |

**Export endpoints (CSV attachments):**

| Report | Export API |
|--------|------------|
| Order lookup | Order lookup export endpoint (see Swagger) |
| Card balance | Card balance export endpoint (see Swagger) |
| Stock transactions | CSV/Excel export on transactions report |

Inspect exact query parameters and response schemas in Swagger: http://localhost:5189/swagger

---

## Stock API paths (non-report)

| Area | API prefix |
|------|------------|
| Main stock | `/api/stock/main/*` |
| Foreign stock | `/api/stock/foreign/*`, `/api/Configuration/*` |
| Third-party | `/api/stock/thirdparty/*` |
| International | `/api/stock/international/*` |
| Import | `/api/import*` |
| Export | `/api/export*` |

---

## Third-party stock API

| SPA area | API |
|----------|-----|
| Third-party config list | `GET /api/stock/thirdparty/config` |
| Products | `GET /api/stock/thirdparty/products?providerId=` |
| Balance | `GET /api/stock/thirdparty/balance?providerId=` |
| Orders | `GET /api/stock/thirdparty/orders?providerId=` |
| Transactions | `GET /api/stock/thirdparty/transactions?providerId=` |
| Refill | `POST /api/stock/refill`, `GET /api/stock/refill/{jobId}` |
| Templates | `/api/stock/templates/*` |

Remember: `providerId` = **ThirdPartyProviderConfigs.Id** — see [api-conventions.md](api-conventions.md).

---

## Detailed integration guide

For request/response JSON examples (authentication, reports, third-party, refill templates):

`voucher.management/docs/WEB-DEVELOPER-API-INTEGRATION.md`

---

## Newman / Postman

API regression collection: `postman/` — run via:

```powershell
pwsh ./scripts/run-tests.ps1 -SkipDockerStart
```

Default target: `http://localhost:5189`
