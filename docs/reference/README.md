# Service reference index

Per-microservice lookup tables for **configuration**, **database schema**, **OpenAPI/Swagger**, and **API route prefixes**.

---

## Services at a glance

| Service | Role | Database | Auth | Reference |
|---------|------|----------|------|-----------|
| **Management API** | Admin stock, imports/exports, reports, third-party | `Voucher.Management.Db.Reclaim` | JWT Bearer + permissions | [management-api.md](management-api.md) |
| **External API** | Bank-facing stock, async reservations | `Voucher.External.Db` | `X-Api-Key` | [external-api.md](external-api.md) |
| **Internal API** | Per-bank stock (6 instances in compose) | `Voucher.Internal.*.Db.Reclaim` | `X-Api-Key` | [internal-api.md](internal-api.md) |
| **Gateway** | Mobile/channel BFF (catalog, reserve, purchase) | `Voucher.Gateway` | JWT Bearer | [gateway.md](gateway.md) |
| **Purchase orchestrator** | Bundle & international purchase sagas | `Voucher.PurchaseOrchestrator` | `X-Api-Key` | [purchase-orchestrator.md](purchase-orchestrator.md) |
| **vouchers-stock SPA** | Angular management UI | — (calls Management API) | JWT via Management | [vouchers-stock-spa.md](vouchers-stock-spa.md) |

---

## Database schema (columns and defaults)

Per-service column definitions extracted from `PostgresInitial` migrations (June 2026).

| Service | Schema reference | Service doc |
|---------|------------------|-------------|
| Management | [schema/management-db-schema.md](schema/management-db-schema.md) | [management-api.md](management-api.md) |
| External | [schema/external-db-schema.md](schema/external-db-schema.md) | [external-api.md](external-api.md) |
| Internal (per bank) | [schema/internal-db-schema.md](schema/internal-db-schema.md) | [internal-api.md](internal-api.md) |
| Gateway | [schema/gateway-db-schema.md](schema/gateway-db-schema.md) | [gateway.md](gateway.md) |
| Purchase orchestrator | [schema/purchase-orchestrator-db-schema.md](schema/purchase-orchestrator-db-schema.md) | [purchase-orchestrator.md](purchase-orchestrator.md) |

---

## Cross-cutting reference

| Topic | Document |
|-------|----------|
| `CustomResult` JSON shape, `providerId`, error codes | [api-conventions.md](api-conventions.md) |
| SPA routes → Management API report paths | [reports-api-guide.md](reports-api-guide.md) |
| Primo Wallet timestamp conventions | [primo-wallet-timestamps.md](primo-wallet-timestamps.md) |
| gRPC remote stock contract | `grpc/stock_remote_v1.proto` (repo root) |

---

## OpenAPI / Swagger — all services

No checked-in static OpenAPI files exist. Every backend exposes **runtime Swashbuckle** when enabled:

| Service | Swagger UI | OpenAPI JSON |
|---------|------------|--------------|
| Management | `/swagger` | `/swagger/v1/swagger.json` |
| External | `/swagger` | `/swagger/v1/swagger.json` |
| Internal | `/swagger` | `/swagger/v1/swagger.json` |
| Gateway | `/swagger` | `/swagger/v1/swagger.json` |
| Orchestrator | `/swagger` | `/swagger/v1/swagger.json` |

**Export a spec for QA:** With the stack running, download JSON from each service's `/swagger/v1/swagger.json` URL.

---

## Connection string keys

| Service | Config key | Default database name |
|---------|------------|----------------------|
| Gateway | `ConnectionStrings:DefaultConnection` | `Voucher.Gateway` |
| External | `ConnectionStrings:Database` | `Voucher.External.Db` |
| Internal | `DbOptions:ConnectionString` | Per-bank |
| Management | `DbOptions:ConnectionString` | `Voucher.Management.Db.Reclaim` |
| Orchestrator | `ConnectionStrings:SagaDbConnection` | `Voucher.PurchaseOrchestrator` |

All services target **PostgreSQL** (Postgres-only migration, June 2026). Remote bank stock accessed from Management via **gRPC only** — not direct SQL.
