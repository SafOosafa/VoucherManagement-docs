# Management API reference

**Project:** `voucher.management/Mitf.Voucher.Stock.Api`  
**Compose service:** `mitf.voucher.stock.api`  
**Image:** `mitf-voucher-stock-api`  
**Local URL:** http://localhost:5189  
**Database:** `Voucher.Management.Db.Reclaim` (PostgreSQL)

Admin and stock-management API for the vouchers-stock SPA. Handles main stock, foreign (bank) stock registration, third-party integrations, reports, import/export, and user auth.

---

## Configuration (`appsettings.json`)

| Section | Key fields | Purpose |
|---------|------------|---------|
| `ConfigSource` | `Type`, `ApplicationName` (`mitf-voucher-stock-api`) | Config source (appsettings or Consul) |
| `DbOptions` | `ConnectionString`, `Provider` | Primary Postgres DB |
| `InternalLoggerOptions` | `Enabled`, `LogType`, `ConnectionString`, `TableName` | Structured API logging |
| `ManagementDataSeed` | `Enabled`, `IncludeThirdPartyProviders`, `IncludeDockerBankForeignStocks`, … | Dev/demo seed (users, Primo, bank stocks) |
| `Auth` | `AllowEasyPass`, `EasyPass` | Dev-only password bypass |
| `RemoteDatabase` | `Provider`, retry/timeouts | Resilience for remote DB metadata (not direct queries) |
| `RemoteStockGrpc` | `Enabled`, `DeadlineSeconds`, `SkipServerCertificateValidation` | gRPC gateway to bank internal APIs |
| `Encryption` | `Key` | Voucher export/import encryption |
| `Currencies` | array | Supported currency codes |
| `Jwt` | `Key`, `Issuer`, `Audience`, `ExpiryMinutes`, `RefreshTokenExpiryMinutes` | SPA JWT auth |
| `Observability` | `Enabled`, `CollectorUrl`, tracing/metrics flags | Mitf.Observability (off by default) |
| `ThirdPartyHttp` | `SkipServerCertificateValidation` | TLS for third-party HTTPS clients |
| `Smtp` | `Host`, `Port`, credentials, `FromEmail` | Low-stock / user temp-password email |
| `ThirdPartyResilience` | retry/circuit breaker | Polly for third-party stock calls |
| `BackgroundServiceSettings` | interval | Scheduled background jobs |
| `LowStockMonitor` | polling config | Low-stock threshold monitoring |
| `OriginsOption` | `AllowedOrigins[]` | **CORS** — must match exact browser origin (scheme + host + port) |
| `swagger` | `title`, `version`, `IsActive`, `BaseUrl` | Swashbuckle metadata |

**Overlay files**

| File | Sections |
|------|----------|
| `appsettings.Development.json` | Local overrides |
| `thirdparty-providers.json` | `ThirdPartyStockOptions`, `ThirdPartyProviders[]` (e.g. Primo Wallet) |

**Connection string key:** `DbOptions:ConnectionString`

---

## Database tables (`MainStockDb`)

Migration: `Mitf.Voucher.Infrastructure/Migrations/20260606184943_PostgresInitial.cs` (26 tables)

| Table | Entity | Purpose |
|-------|--------|---------|
| `Providers` | `Provider` | Telecom/provider catalog (`Id`, business `ProviderId`, `Name`) |
| `VoucherTypes` | `VoucherType` | Denominations per provider |
| `Vouchers` | `VoucherData` | Main stock inventory (`SerialNo`, `Secret`, `State`) |
| `MainStockArchive` | `MainStockVoucherArchive` | Archived vouchers after export/use |
| `BundleDefinitions` | `BundleDefinition` | Bundle offer metadata |
| `BundleComponents` | `BundleComponent` | Bundle line items |
| `BundleVouchers` | `BundleVoucher` | Bundle voucher stock |
| `BundleVoucherArchives` | `BundleVoucherArchive` | Archived bundle vouchers |
| `ForeignStocks` | `ForeignStock` | Remote bank DB registry (`ConnectionString`, `ApiUrl` for gRPC) |
| `ImportRequests` | `ImportRequest` | Voucher import batches |
| `ImportOrders` | `ImportOrder` | Import line items |
| `ExportOrders` | `ExportRequest` | Export to bank stock |
| `ExportBundleRequests` | `ExportBundleRequest` | Bundle export requests |
| `CustodyTransfers` | `CustodyTransfer` | Branch-to-branch custody |
| `UploadedFiles` | `UploadedFiles` | Import file metadata |
| `SystemConfigurationRules` | `SystemConfigurationRule` | Enable/disable entities per bank |
| `Users` | `User` | Admin users, roles, permissions |
| `RefreshTokens` | `RefreshToken` | JWT refresh tokens |
| `UserActivities` | `UserActivity` | Audit trail |
| `ThirdPartyProviderConfigs` | `ThirdPartyProviderConfig` | Third-party provider config (`ProviderType`, `BaseUrl`, JSON config) |
| `InternationalVouchers` | `InternationalVoucherData` | International third-party stock |
| `InternationalStockArchives` | `InternationalStockArchive` | Archived international stock |
| `StockRefillRequests` | `StockRefillRequestEntity` | Third-party refill jobs |
| `StockRefillTemplates` | `StockRefillTemplate` | Refill templates |
| `InternalStockThresholdRules` | `InternalStockThresholdRule` | Low-stock rules per bank/provider |
| `InternalStockLowAlerts` | `InternalStockLowAlert` | Triggered low-stock alerts |

**Remote bank schema** (`RemoteStockDb` — no migrations in management; same shape as internal API DB):

`Providers`, `VoucherTypes`, `Vouchers`, `Transactions`, `BundleDefinitions`, `BundleComponents`, `BundleVouchers`, `EnabledEntities`

### Column properties and defaults

Full column definitions (CLR type, nullable, server default): **[schema/management-db-schema.md](schema/management-db-schema.md)**

**PostgreSQL server defaults**

| Table | Column | Default |
|-------|--------|---------|
| `ExportOrders` | `TransferDate` | `CURRENT_TIMESTAMP` |
| `MainStockArchive` | `CreatedDate` | `CURRENT_TIMESTAMP` |
| `Vouchers` | `CreatedDate` | `CURRENT_TIMESTAMP` |

All other columns have no server default (`—` in the schema doc). Primary keys use PostgreSQL identity.

**Seed data:** `Users` has migration `InsertData` (admin user). Runtime demo data is controlled by `ManagementDataSeed` in appsettings.

**Remote bank columns:** Same column layout as **[schema/internal-db-schema.md](schema/internal-db-schema.md)** (accessed via gRPC only, not direct SQL).

---

## Swagger / OpenAPI

| Item | Value |
|------|-------|
| UI | `/swagger` |
| JSON | `/swagger/v1/swagger.json` |
| Title | Api Management (v1) |
| Static spec in repo | **None** — runtime Swashbuckle |
| Registration | Conditional on DEBUG in Docker for local Swagger |

**Health / metrics**

| Endpoint | Behavior |
|----------|----------|
| `GET /health/ready` | **200** when migrate+seed complete; **503** while seeding |
| `GET /health`, `GET /ping` | Liveness |
| `GET /metrics` | Prometheus (requires `Observability:Enabled=true`) |

---

## API route prefixes

| Prefix | Purpose |
|--------|---------|
| `/api/authentication/*` | Login, MFA, refresh, profile, logout |
| `/api/users/*` | User CRUD, password reset, activities |
| `/api/permissions/*` | Permission listing |
| `/api/providers` | Provider CRUD |
| `/api/stock/main/*` | Main stock, bundles, import/export requests |
| `/api/stock/foreign/*` | Foreign stock registration |
| `/api/stock/thirdparty/*` | Third-party balance, orders, transactions, config |
| `/api/stock/international/*` | International inventory and pricing |
| `/api/stock/templates/*`, `/api/stock/refill/*` | Refill templates and jobs |
| `/api/stock/low-stock/*` | Threshold rules and alerts |
| `/api/stock/branch-transfer/*` | Branch transfers |
| `/api/stock/reclaim` | Stock reclaim |
| `/api/reports/*` | Card balance, order lookup, liability, cards loaded |
| `/api/import*`, `/api/export*` | Import/export vouchers and bundles |
| `/api/bundle-imports`, `/api/bundle-exports`, `/api/bundle-definitions/*` | Bundle workflows |
| `/api/Configuration/*` | Remote stock configuration rules |
| `/api/logs`, `/api/getCurrencies` | Ops utilities |

**Auth:** JWT Bearer + permission policies (`Permissions.*`).

**Response shape:** `CustomResult` / `CustomResult<T>` — see [api-conventions.md](api-conventions.md).
