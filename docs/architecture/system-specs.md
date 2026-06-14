# System specifications

Platform specification for the MITF Voucher Provider workspace.

---

## Purpose

Digital voucher stock management for telecom providers and bank partners:

- **Central stock** — Import, custody, export to bank systems
- **Bank stock** — Per-bank internal APIs holding exported vouchers
- **Channel purchase** — Gateway and external API reservation/confirm flows
- **Bundle/international purchase** — Orchestrated sagas across internal + bank APIs
- **Third-party stock** — Primo Wallet and similar upstream catalogs
- **Admin SPA** — Angular management UI (`vouchers-stock`)

---

## Solution roots (monorepo)

| Path | Runtime | Database |
|------|---------|----------|
| `voucher.management` | Management API + SPA backend | `Voucher.Management.Db.Reclaim` |
| `voucher.external` | Bank-facing stock + reservations | `Voucher.External.Db` |
| `voucher.internal` | Per-bank stock (x6 in compose) | One DB per bank |
| `Voucher.Gateway` | Mobile/channel BFF | `Voucher.Gateway` |
| `voucher.purchaseorchestrator` | Purchase sagas | `Voucher.PurchaseOrchestrator` |
| `vouchers-stock` | Angular SPA | None (HTTP client) |

---

## Branches

- `main` — Production
- `dev` — Integration

---

## Target framework

.NET 10.0 on all API hosts

---

## Infrastructure dependencies

| Component | Version | Used by |
|-----------|---------|---------|
| PostgreSQL | 16 | All APIs |
| Redis | 7 | External API counters |
| RabbitMQ | 3.13 | External reservations, orchestrator sagas |
| gRPC | — | Management to Internal bank APIs on port 8081 |

Optional: Consul, Loki, Tempo, Prometheus, Grafana, OTEL collector

---

## Data boundaries

- No shared database across microservices
- Management stores `ForeignStocks` registry (connection metadata + gRPC URL)
- Remote bank data accessed via gRPC only
- Voucher secrets encrypted at export with per-target keys
- Third-party provider configs in `ThirdPartyProviderConfigs` table

---

## Authentication

| Surface | Mechanism |
|---------|-----------|
| Management SPA + API | JWT Bearer + permission policies |
| External / Internal / Orchestrator | `X-Api-Key` |
| Gateway | JWT Bearer for mobile clients |

---

## Published Docker images

| Image | Source |
|-------|--------|
| `mitf-voucher-stock-api` | Management API |
| `mitf-voucher-external-api` | External API |
| `mitf-voucher-internal-api` | Internal API |
| `mitf-voucher-gateway` | Gateway |
| `mitf-voucher-purchase-orchestrator` | Purchase orchestrator |
| `mitf-vouchers-stock-web` | SPA |

---

## QA verification tiers

| Layer | Tool | Scope |
|-------|------|--------|
| API | Newman / Postman | Contracts, auth, business rules |
| UI (CRUD) | Playwright | Full feature flows with tags |
| UI (smoke) | Maestro | Read-only route smoke |

---

## Platform constraints

- Pin `Destructurama.JsonNet` **2.0.1** (3.0.0 causes Linux crashes)
- CORS exact origins on Management API
- Recreate stock API container after env change (`docker compose restart` may leave stale env)
- PowerShell 7+ for QA scripts
- Use `127.0.0.1` for Cursor browser automation on Windows

---

## Related pages

- [Workspace overview](workspace-overview.md)
- [Production deployment](../operations/production-deployment.md)
- [Service reference index](../reference/)
