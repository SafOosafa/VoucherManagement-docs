# Internal API reference

**Project:** `voucher.internal/Mitf.Voucher.Internal.Api`
**Compose services:** `mitf.voucher.internal.jumhoria` through `mitf.voucher.internal.alwaha` (6 banks)
**Image:** `mitf-voucher-internal-api`
**Local URLs:** http://localhost:5238-5243
**Database:** One Postgres DB per bank instance

Per-bank stock API. Bundle reserve/confirm, international vouchers, reclaim, and gRPC `StockRemoteBridge` for management export/report operations.

---

## Configuration (`appsettings.json`)

| Section | Key fields | Purpose |
|---------|------------|---------|
| `ConfigSource` | `ApplicationName` (`mitf-voucher-internal-api`) | Config source identity |
| `DbOptions` | `ConnectionString`, `Provider` | Per-bank Postgres DB |
| `InternalLoggerOptions` | logging config | Structured request logging |
| `Encryption` | `Key` | Voucher decrypt (must match management export key) |
| `ReservationCleanupSettings` | TTL/cleanup | Expired reservation cleanup |
| `Observability` | OTEL/metrics | Mitf.Observability |
| `swagger` | `IsActive`, `BaseUrl` | Swashbuckle |
| `ProviderDisplayOptions` | colors, provider IDs | UI display metadata |

**Overlay:** `appsettings.Development.json` (logging only)

**Connection string key:** `DbOptions:ConnectionString`

---

## Docker bank instances

| Compose service | Host port | Database name |
|-----------------|-----------|---------------|
| `mitf.voucher.internal.jumhoria` | 5238 | `Voucher.Internal.Jumhoria.Db.Reclaim` |
| `mitf.voucher.internal.tejari` | 5239 | `Voucher.Internal.Tejari.Db.Reclaim` |
| `mitf.voucher.internal.sahara` | 5240 | `Voucher.Internal.Sahara.Db.Reclaim` |
| `mitf.voucher.internal.daman` | 5241 | `Voucher.Internal.Daman.Db.Reclaim` |
| `mitf.voucher.internal.alsiraj` | 5242 | `Voucher.Internal.Alsiraj.Db.Reclaim` |
| `mitf.voucher.internal.alwaha` | 5243 | `Voucher.Internal.Alwaha.Db.Reclaim` |

**Kestrel:** HTTP 8080, gRPC 8081 (internal to compose network)

---

## Database tables (`StockDbContext`)

Migration: `Mitf.Voucher.Internal.Infrastructure/Migrations/20260606184534_PostgresInitial.cs` (10 tables)

| Table | Entity | Purpose |
|-------|--------|---------|
| `Providers` | `Provider` | Provider catalog (`Brand`, `Region`) |
| `VoucherTypes` | `VoucherType` | Denominations; `ManagementVoucherTypeId`, `IsInternational` |
| `Vouchers` | `VoucherData` | Bank-local active stock |
| `VouchersArchive` | `VoucherArchive` | Archived vouchers |
| `Transactions` | `Transaction` | Channel transactions |
| `BundleDefinitions` | `BundleDefinition` | Bundle offers |
| `BundleComponents` | `BundleComponent` | Bundle components |
| `BundleVouchers` | `BundleVoucher` | Bundle voucher stock |
| `BundleTransactions` | `BundleTransaction` | Bundle purchase ledger |
| `EnabledEntities` | `EnabledEntity` | Per-entity enablement |

### Column properties and defaults

Full column definitions: **[schema/internal-db-schema.md](schema/internal-db-schema.md)**

No PostgreSQL server defaults in the initial migration — all columns use `—` unless set by application code. Primary keys use PostgreSQL identity.

This schema is replicated per bank internal API instance (Jumhoria, Wahda, etc.) and mirrored remotely from Management via gRPC.

---

## Swagger / OpenAPI

| Item | Value |
|------|-------|
| UI | `/swagger` |
| JSON | `/swagger/v1/swagger.json` |
| Static spec in repo | None (runtime Swashbuckle) |
| gRPC | `InternalStockRemoteBridgeGrpcService` - see `grpc/stock_remote_v1.proto` |

---

## API route prefixes

| Prefix | Purpose |
|--------|---------|
| `/api/stock/*` | Items, reclaim, international, user history |
| `/api/voucher/*` | confirm-or-cancel, reverse, international variants |
| `/api/bundles/*` | reserve, confirm, bundle stock |
| `/api/providers/*` | Bundle providers |
| `/api/Lists/Vouchers` | Legacy list endpoint |

**Auth:** `X-Api-Key` header.

**Playwright:** `@internal` tag targets Jumhoria at http://127.0.0.1:5238