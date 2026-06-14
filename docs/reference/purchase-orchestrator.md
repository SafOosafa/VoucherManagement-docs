# Purchase orchestrator reference

**Project:** `voucher.purchaseorchestrator/Voucher.PurchaseOrchestrator.Api`  
**Compose service:** `mitf.voucher.purchaseorchestrator.api`  
**Image:** `mitf-voucher-purchase-orchestrator`  
**Profile:** `orchestrator` (not started by default compose)  
**Local URL:** http://localhost:5260  
**Database:** `Voucher.PurchaseOrchestrator` (PostgreSQL + MassTransit saga persistence)

Coordinates bundle and international purchase sagas across Internal API (stock reserve/confirm) and External/bank API (financial transaction).

---

## Configuration (`appsettings.json`)

| Section | Key fields | Purpose |
|---------|------------|---------|
| `ConfigSource` | `ApplicationName` (`mitf-voucher-purchase-orchestrator`) | Config source identity |
| `ConnectionStrings` | `SagaDbConnection` | Saga + idempotency Postgres DB |
| `DatabaseSettings` | `Provider` | Database provider |
| `LogOptions` | SQL sink connection | Semi-structured saga logging |
| `ConsumerRetrySettings` | retries | MassTransit consumer policy |
| `RabbitMqOptions` | host, vhost, credentials | RabbitMQ / MassTransit |
| `ApiSettings` | `StockApiUrl`, `BankApiUrl`, control panel URL | Downstream HTTP targets |
| `BankProviderMappings` | product ID mappings | Internal ↔ bank product IDs |
| `BankTransaction` | retry policy | Bank API resilience |
| `InternalLoggerOptions` | enabled flag | Optional request logging |
| `Observability` | OTEL/metrics | Mitf.Observability |
| `Idempotency` | retention, cleanup | Purchase dedup records |

**Connection string key:** `ConnectionStrings:SagaDbConnection`

**Compose defaults:**

| Setting | Value |
|---------|-------|
| `ApiSettings__StockApiUrl` | `http://mitf.voucher.internal.jumhoria:8080` |
| `ApiSettings__BankApiUrl` | `http://mitf.voucher.external.api:8080` |

---

## Database tables (`PurchaseOrchestratorSagaDbContext`)

Migration: `Voucher.PurchaseOrchestrator.Infrastructure/Migrations/20260606184636_PostgresInitial.cs` (8 tables)

| Table | Entity | Purpose |
|-------|--------|---------|
| `BundleSagaState` | `BundleSagaState` | MassTransit bundle purchase saga state |
| `InternationalSagaState` | `InternationalSagaState` | International purchase saga state |
| `BundlePurchaseArchives` | `BundlePurchaseArchive` | Completed bundle outcomes |
| `InternationalPurchaseArchives` | `InternationalPurchaseArchive` | Completed international outcomes |
| `PurchaseIdempotencyRecords` | `PurchaseIdempotencyRecord` | HTTP dedup on `(ChannelId, RequestId)` |
| `TransactionSequences` | `TransactionSequence` | Sequence generation |
| `StatisticDoc` | `StatisticDoc` | JSON statistics documents |
| `Logs` | `LogEntry` | Request/response logging |

### Column properties and defaults

Full column definitions: **[schema/purchase-orchestrator-db-schema.md](schema/purchase-orchestrator-db-schema.md)**

No PostgreSQL server defaults in the initial migration — saga state columns are populated by MassTransit at runtime.

---

## Swagger / OpenAPI

| Item | Value |
|------|-------|
| UI | `/swagger` |
| JSON | `/swagger/v1/swagger.json` |
| appsettings `swagger` section | **Not present** — default Swashbuckle registration |
| Static spec in repo | **None** |

---

## API route prefixes

| Method | Path | Purpose |
|--------|------|---------|
| POST | `/api/purchase-requests` | Start bundle purchase saga |
| GET | `/internal/purchase-results/{correlationId}` | Poll purchase outcome |
| GET | `/internal/users/{userId}/purchase-history` | User purchase history |
| POST | `/api/test/international/start-saga` | Test international saga |
| GET | `/` | Health text endpoint |

**Auth:** `X-Api-Key` header.

**Downstream HTTP (orchestrator clients):**

| Target | Endpoints |
|--------|-----------|
| Internal (`StockApiUrl`) | `/api/bundles/reserve`, `/api/bundles/confirm`, international voucher endpoints |
| Bank (`BankApiUrl`) | `/api/Transaction/BuyVoucher` |

**Saga states:** `Initial` → `AwaitingReservation` → `AwaitingBankTransaction` → `AwaitingConfirmation` → `AwaitingArchiving` → `Finalized`

**Messaging contracts:** `voucher.purchaseorchestrator/Voucher.Messaging.Contracts/`
