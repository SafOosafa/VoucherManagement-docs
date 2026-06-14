# Production deployment

## Compose files

| File | Purpose |
|------|---------|
| `docker-compose.yml` | Local dev — build from source |
| `docker-compose.qa.yml` | QA — pull Hub images |
| `docker-compose.ci.yml` | CI overlay — trim seed, force build |
| `docker-compose.override.yml` | Local overrides |

```powershell
docker compose up --build
docker compose --profile orchestrator up --build
docker compose --profile observability up -d
docker compose -f docker-compose.qa.yml up -d
```

---

## Services, images, and ports

| Compose service | Image | Host port |
|-----------------|-------|-----------|
| `mitf.voucher.stock.api` | `mitf-voucher-stock-api` | 5189 |
| `mitf.voucher.external.api` | `mitf-voucher-external-api` | 5252 |
| `mitf.voucher.internal.*` (x6) | `mitf-voucher-internal-api` | 5238–5243 |
| `mitf.voucher.gateway` | `mitf-voucher-gateway` | 5089 |
| `mitf.voucher.purchaseorchestrator.api` | `mitf-voucher-purchase-orchestrator` | 5260 (profile) |
| `vouchers-stock-web` | `mitf-vouchers-stock-web` | 4200 |
| `postgres` | `postgres:16-alpine` | 5432 |
| `redis` | `redis:7-alpine` | 6379 |
| `rabbitmq` | `rabbitmq:3.13-management-alpine` | 5672, 15672 |

---

## Compose profiles

| Profile | Adds |
|---------|------|
| (default) | Core apps + postgres/redis/rabbitmq |
| `orchestrator` | Purchase orchestrator |
| `observability` | Consul, Loki, Tempo, Prometheus, Grafana, OTEL |

---

## Environment variables

| Variable | Purpose |
|----------|---------|
| `DOCKER_REGISTRY` | Pull Hub images (trailing slash) |
| `STOCK_CORS_ORIGIN_0..3` | Management CORS origins |
| `STOCK_WEB_BASE_URL` | SPA API base URL |
| `ManagementDataSeed__Enabled` | Dev login + demo data |
| `OBSERVABILITY_ENABLED` | Enable OTEL on APIs |
| `RabbitMqOptions__RabbitMqVirtualHost` | Default `voucher` |

After Management API env changes: `docker compose up -d --force-recreate mitf.voucher.stock.api`

---

## Health checks

| Service | Endpoint | Notes |
|---------|----------|-------|
| Management | `GET /health/ready` | 503 until migrate+seed done |
| Management | `GET /health`, `GET /ping` | Liveness |
| Management | `GET /metrics` | Requires Observability enabled |

---

## GitHub Actions

**docker-hub.yml** — publishes `mitf-voucher-*` images on push to `dev`/`main`

**qa-regression.yml** — unit tests, compose stack, Newman, Playwright smoke

---

## QA scripts (PowerShell 7+)

| Script | Role |
|--------|------|
| `scripts/start-qa-stack.ps1` | Start stack + wait for ready |
| `scripts/run-tests.ps1` | Newman API regression |
| `scripts/run-playwright-tests.ps1` | Playwright UI |
| `scripts/run-ui-tests.ps1` | Maestro smoke |
| `scripts/run-all-tests.ps1` | Full pyramid |

```powershell
pwsh ./scripts/start-qa-stack.ps1 -IncludeWeb
pwsh ./scripts/run-tests.ps1 -SkipDockerStart
pwsh ./scripts/run-playwright-tests.ps1 -SkipDockerStart -Grep "@smoke"
```

---

## Server specifications

| Component | CPU | Memory |
|-----------|-----|--------|
| Management API | 2 vCPU | 2 GB RAM |
| External API | 2 vCPU | 2 GB RAM |
| Internal API (per bank) | 1 vCPU | 1 GB RAM |
| Gateway | 1 vCPU | 1 GB RAM |
| Orchestrator | 1 vCPU | 1 GB RAM |
| PostgreSQL | 6 vCPU | 8 GB RAM |
| RabbitMQ | 2 vCPU | 2 GB RAM |
| Redis | 1 vCPU | 1 GB RAM |

---

## Port matrix

| Service | HTTP | gRPC |
|---------|------|------|
| Management | 8080 | — |
| External | 8080 | 8081 |
| Internal (per bank) | 8080 | 8081 |
| Gateway | 8080 | — |
| Orchestrator | 8080 | — |

---

## Network and firewall

| Tier | Ports | Protocol |
|------|-------|----------|
| DMZ | 443, 80 | HTTPS |
| Application | 8080, 8081 | HTTP, gRPC |
| Backend | 5432, 6379, 5672, 15672 | PostgreSQL, Redis, RabbitMQ |

---

## Database layout

| Database | Service |
|----------|---------|
| `Voucher.Management.Db.Reclaim` | Management |
| `Voucher.External.Db` | External |
| `Voucher.Internal.*.Db.Reclaim` | Internal (per bank) |
| `Voucher.Gateway` | Gateway |
| `Voucher.PurchaseOrchestrator` | Orchestrator |

---

## Related docs

- [Local compose and QA environment](local-compose-and-qa.md)
- [Observability and Consul](logging.md)
- [Logging](logging.md)
