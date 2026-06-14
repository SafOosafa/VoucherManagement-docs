# Gateway reference

**Project:** `Voucher.Gateway/Mitf.VoucherProvider`  
**Compose service:** `mitf.voucher.gateway`  
**Image:** `mitf-voucher-gateway`  
**Local URL:** http://localhost:5089  
**Database:** `Voucher.Gateway` (PostgreSQL)

BFF for mobile/channel clients. Exposes JWT-protected voucher catalog, reserve, and purchase endpoints. Proxies to upstream stock provider (configured as Internal or External URL per environment).

---

## Configuration (`appsettings.json`)

| Section | Key fields | Purpose |
|---------|------------|---------|
| `ConfigSource` | `ApplicationName` (`mitf-voucher-gateway`) | Config source identity |
| `ConnectionStrings` | `DefaultConnection` | Gateway Postgres DB |
| `StockProviderServiceLink` | base URL | Upstream stock API (compose default: internal Jumhoria) |
| `ImagePathOption` | path, public host URL | Provider image storage |
| `Jwt` | `SecretKey`, issuer, audience, lifetimes | Consumer JWT auth |
| `DatabaseSettings` | `Provider` | Postgres / SqlServer / InMemory |
| `LogOptions` | Serilog SQL sink | Legacy structured logging |
| `swagger` | `IsActive`, `BaseUrl` | Swashbuckle |
| `InternalLoggerOptions` | enabled flag | Optional request logging middleware |
| `Observability` | OTEL/metrics | Mitf.Observability |

**Overlay:** `appsettings.Development.json` (logging only)

**Connection string key:** `ConnectionStrings:DefaultConnection`

**Shared base (optional):** `APITokenDb` in `Voucher.Gateway/Shared Base/Mitf.WebAPI/` — outbound API token store (defaults to in-memory)

---

## Database tables (`DataContext`)

Migration: `Mitf.Voucher.Services/Migrations/20260606185040_PostgresInitial.cs` (2 tables)

| Table | Entity | Purpose |
|-------|--------|---------|
| `Users` | `UserModel` | Gateway consumer auth |
| `RefreshTokens` | `RefreshToken` | JWT refresh tokens |

**APITokenDb (shared, optional in-memory):**

| Table | Entity | Purpose |
|-------|--------|---------|
| `APIInstances` | `APIInstance` | Outbound API tokens per `EntryAPI` enum |

### Column properties and defaults

Full column definitions: **[schema/gateway-db-schema.md](schema/gateway-db-schema.md)**

**PostgreSQL server defaults:** none in the initial migration.

**Seed data:** `Users` has migration `InsertData` (default admin user with bcrypt password hash, `CreationDate` = `2024-01-01`).

`APITokenDb` tables are optional/in-memory and are not part of the Gateway Postgres migration.

---

## Swagger / OpenAPI

## Swagger / OpenAPI

| Item | Value |
|------|-------|
| UI | `/swagger` |
| JSON | `/swagger/v1/swagger.json` |
| Static spec in repo | **None** |

---

## API route prefixes

| Prefix | Purpose |
|--------|---------|
| `/api/auth/*` | `accessToken`, `refreshToken` |
| `/api/voucher/*` | `providers`, `vouchers`, `reserve`, `handel-purchase` |
| `/image/*` | Static provider images |
| `/metrics` | Prometheus (when observability enabled) |

**Auth:** JWT Bearer.

**Downstream mapping (via `StockProvider` client):**

| Gateway | Upstream |
|---------|----------|
| `POST api/voucher/reserve` | `POST /api/reserve` |
| `POST api/voucher/handel-purchase` | `POST /api/handle-purchase` |
| Provider/voucher lists | `/api/providers`, `/api/vouchers` |
