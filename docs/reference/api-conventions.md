# API conventions

Cross-service conventions for integrators and QA when reading responses or calling Management third-party endpoints.

---

## CustomResult response shape

`voucher.management` (and several other services) return JSON wrapped in `CustomResult` / `CustomResult<T>`:

```json
{
  "success": true,
  "message": "success",
  "content": { }
}
```

| Field | Type | Meaning |
|-------|------|---------|
| `success` | boolean | Operation succeeded |
| `message` | string | Human-readable status or error summary |
| `content` | object or array | Payload when `success` is true; may be null on failure |

**QA tip:** Many report endpoints nest the actual data under `content`. Newman and Playwright helpers in this repo parse that shape.

---

## Third-party `providerId`

On Management third-party stock endpoints, **`providerId` is the third-party provider config row id** (`ThirdPartyProviderConfigs.Id`), not necessarily the business telecom `ProviderId`.

| Concept | Field | Table |
|---------|-------|-------|
| Config id (use in API query params) | `Id` | `ThirdPartyProviderConfigs` |
| Business provider id | `ProviderId` | `Providers` |

List configs: `GET /api/stock/thirdparty/config`

---

## Common stock error codes

| Code | When |
|------|------|
| `IMPORT_DUPLICATE_SECRET` | CSV import contains duplicate voucher secrets |
| `ORDER_ID_EDIT_NOT_ALLOWED_STATE` | Order id edit blocked after activation |
| `EXPORT_INSUFFICIENT_STOCK` | Export/transfer quantity exceeds available stock |
| `CUSTODY_TRANSFER_BLOCKED_MONTHLY_OPEN_TRANSFER` | Monthly open custody transfer rule violated |

UAT checklist: [../flows/uat-checklist-voucher-stock.md](../flows/uat-checklist-voucher-stock.md)

---

## Authentication schemes

| Service | Scheme | Header |
|---------|--------|--------|
| Management API | JWT Bearer | `Authorization: Bearer <token>` |
| External API | API key | `X-Api-Key` |
| Internal API | API key | `X-Api-Key` |
| Gateway | JWT Bearer | `Authorization: Bearer <token>` |
| Purchase orchestrator | API key | `X-Api-Key` |

**Dev login** (Management, when seed enabled): `admin` / `admin`

---

## CORS (Management API)

Browser calls from the SPA send an **exact origin** (scheme + host + port). Configure `OriginsOption:AllowedOrigins` in Management appsettings or compose env (`STOCK_CORS_ORIGIN_0..3`).

A missing origin often surfaces as a **CORS error in the browser** even when Postman or Swagger on the same host succeeds.

---

## Platform notes

| Topic | Value |
|-------|-------|
| Target framework | **net10.0** on all API hosts |
| Observability | **Mitf.Observability** — off by default; enable via compose profile |
| Destructurama.JsonNet | Pin **2.0.1** everywhere (not 3.0.0) — mixed versions cause Linux container crashes |
| Database | **PostgreSQL** only (PostgresInitial migrations, June 2026) |
| Remote bank stock | **gRPC only** from Management — no direct remote SQL contexts |
