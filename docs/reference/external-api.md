# External API reference

**Project:** `voucher.external/Mitf.Voucher.External.Api`  
**Compose service:** `mitf.voucher.external.api`  
**Image:** `mitf-voucher-external-api`  
**Local URL:** http://localhost:5252  
**Database:** `Voucher.External.Db` (PostgreSQL)  
**Also uses:** Redis (voucher counts), RabbitMQ (MassTransit reservations)

Bank-facing stock API. Handles async voucher reservations via MassTransit, sync purchase confirm/cancel, and gRPC remote stock bridge.

---

## Configuration (`appsettings.json`)

| Section | Key fields | Purpose |
|---------|------------|---------|
| `ConfigSource` | `ApplicationName` (`mitf-voucher-external-api`) | Config source identity |
| `ConnectionStrings` | `Database` | Primary Postgres DB |
| `DatabaseSettings` | `Provider` | Database provider (`Postgres`) |
| `InternalLoggerOptions` | logging config | Structured request logging |
| `RedisConfigOptions` | connection, sync interval | Voucher availability counters |
| `EncryptionOptions` | `Key` | Voucher encryption |
| `ReservationRetrySettings` | retries, cleanup TTL | Reservation consumer policy |
| `FetchTransactionRetryOptions` | retries | Transaction fetch resilience |
| `RabbitMqOptions` | host, vhost, credentials, prefetch | MassTransit / RabbitMQ |
| `swagger` | `title` (`External Voucher API`), `IsActive`, `BaseUrl` | Swashbuckle |
| `Observability` | OTEL/metrics flags | Mitf.Observability |

**Kestrel ports (env/code, not appsettings):** HTTP **8080**, gRPC **8081** (internal to compose network)

**Connection string key:** `ConnectionStrings:Database`

---

## Database tables (`StockDb`)

Migration: `Mitf.Voucher.Infrastructure/Migrations/20260606185010_PostgresInitial.cs` (8 tables)

| Table | Entity | Purpose |
|-------|--------|---------|
| `Providers` | `Provider` | Provider catalog (`ProviderId`, `Name`) |
| `VoucherTypes` | `VoucherType` | Denominations; `ManagementVoucherTypeId` links to management |
| `Vouchers` | `VoucherData` | Active stock (`SerialNo`, `Secret`, `State`, `OrderId`) |
| `Transactions` | `Transaction` | Purchase/reservation ledger |
| `ForeignStockArchive` | `ForeignStockVoucherArchive` | Archived vouchers |
| `EnabledEntities` | `EnabledEntity` | Feature toggles |
| `ReservationEvents` | `ReservationEventRecord` | Reservation audit events |
| `ReservationIdempotencyRecords` | `ReservationIdempotencyRecord` | Duplicate reservation replay guard |

### Column properties and defaults

Full column definitions: **[schema/external-db-schema.md](schema/external-db-schema.md)**

**PostgreSQL server defaults**

| Table | Column | Default |
|-------|--------|---------|
| `Vouchers` | `CreatedDate` | `CURRENT_TIMESTAMP` |
| `Transactions` | `CreatedAt` | `CURRENT_TIMESTAMP` |

All other columns have no server default (`—` in the schema doc).

---

## Swagger / OpenAPI

## Swagger / OpenAPI

| Item | Value |
|------|-------|
| UI | `/swagger` |
| JSON | `/swagger/v1/swagger.json` |
| Title | External Voucher API (v1) |
| Static spec in repo | **None** |

**gRPC:** `ExternalStockRemoteBridgeGrpcService` on port **8081** — contract in `grpc/stock_remote_v1.proto`

---

## API route prefixes

| Method | Path | Behavior |
|--------|------|----------|
| POST | `/api/reserve` | Queue async reservation (MassTransit) |
| POST | `/api/handle-purchase` | Sync confirm/cancel on reservation |
| GET | `/api/vouchers`, `/api/providers` | Stock catalog |
| GET | `/api/reservation-events` | Reservation event stream |
| GET | `/api/reports/stock/*` | Usage, history, reservation status |
| GET | `/api/v1/reports/*` | Analytics (sales, revenue, customers) |
| GET | `/api/v1/stock/report` | Current stock report |

**Auth:** `X-Api-Key` header.

**MassTransit queues:** `reservation-requests`, `reservation-event-logger`
