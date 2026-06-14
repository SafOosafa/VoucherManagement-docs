# Gateway database schema

Source migration: `Voucher.Gateway/Mitf.Voucher.Services/Migrations/20260606185040_PostgresInitial.cs`

Primary keys use PostgreSQL identity / serial unless noted. `—` = no server default.

## RefreshTokens

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | int | no | — |
| Token | string | no | — |

## Users

**Seed data:** Has `InsertData` seed row(s) in migration (see source file).

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | int | no | — |
| Username | string | no | — |
| Password | string | no | — |
