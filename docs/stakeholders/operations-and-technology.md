# Operations & technology

## Technology stack

| Component | Technology |
|-----------|------------|
| Runtime | .NET 10.0 (ASP.NET Core) |
| APIs | REST (Swashbuckle) + gRPC |
| Databases | PostgreSQL 16 per service |
| Cache | Redis 7 |
| Messaging | RabbitMQ 3.13 / MassTransit |
| UI | Angular 18+ |
| Containerization | Docker Compose |
| CI / CD | GitHub Actions |
| Observability | OpenTelemetry → Prometheus / Loki / Tempo → Grafana |

---

## Service summary

| Service | Role | Database | Auth |
|---------|------|----------|------|
| Management API | Admin stock, users, reports, third-party | `Voucher.Management.Db.Reclaim` | JWT + permissions |
| External API | Bank-facing stock, async reservations | `Voucher.External.Db` | API key |
| Internal APIs (x6) | Per-bank stock with gRPC bridge | `Voucher.Internal.*.Db.Reclaim` | API key |
| Gateway | Mobile BFF, catalog proxy | `Voucher.Gateway` | JWT |
| Purchase Orchestrator | Saga-based bundle/international purchases | `Voucher.PurchaseOrchestrator` | API key |
| Stock SPA | Angular admin UI | None (HTTP client) | JWT via Management |

---

## Infrastructure architecture

```mermaid
flowchart TB
    subgraph clients [Clients]
        SPA[vouchers-stock SPA :4200]
        Mobile[Mobile / Gateway clients]
        Bank[Bank / channel APIs]
    end

    subgraph apis [API layer]
        MGMT[Management API :5189]
        EXT[External API :5252]
        GW[Gateway :5089]
        INT[Internal APIs :5238-5243]
        ORCH[Purchase Orchestrator :5260]
    end

    subgraph infra [Infrastructure]
        PG[(PostgreSQL)]
        Redis[(Redis)]
        RMQ[RabbitMQ]
    end

    SPA -->|JWT REST| MGMT
    Mobile -->|JWT| GW
    Bank -->|API key| EXT
    GW -->|HTTP| INT
    ORCH -->|HTTP| INT
    ORCH -->|HTTP| EXT
    MGMT -->|gRPC :8081| INT
    MGMT --> PG
    EXT --> PG
    EXT --> Redis
    EXT --> RMQ
    INT --> PG
    GW --> PG
    ORCH --> PG
    ORCH --> RMQ
    MGMT -->|HTTPS| TP[Third-party e.g. Primo]
```

---

## Related pages

- [Production deployment](../operations/production-deployment.md)
- [Logging & observability](../operations/logging.md)
- [Load testing operations](../load-testing/README.md)
