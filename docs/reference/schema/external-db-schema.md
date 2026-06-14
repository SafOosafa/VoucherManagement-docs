# External database schema

Source migration: `voucher.external/Mitf.Voucher.Infrastructure/Migrations/20260606185010_PostgresInitial.cs`

Primary keys use PostgreSQL identity / serial unless noted. `—` = no server default.

## EnabledEntities

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | int | no | — |
| EntityType | int | no | — |

## ForeignStockArchive

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | int | no | — |
| VoucherTypeId | int | no | — |
| Value | decimal | no | — |
| SerialNo | string | no | — |
| Secret | string | no | — |

## Providers

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | int | no | — |
| ProviderId | int | no | — |
| Name | string | no | — |
| Description | string | yes | — |

## ReservationEvents

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | Guid | no | — |
| ReservationId | string | no | — |
| TransactionId | int | yes | — |
| EventType | string | no | — |
| Timestamp | DateTimeOffset | no | — |
| VoucherTypeId | int | yes | — |
| VoucherId | int | yes | — |
| CustomerId | string | yes | — |
| Details | string | yes | — |

## ReservationIdempotencyRecords

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| ReservationId | string | no | — |
| Succeeded | bool | no | — |
| VoucherId | int | yes | — |
| VoucherTypeId | int | no | — |
| Amount | decimal | yes | — |
| ReservedAt | DateTime | yes | — |
| FailureReason | string | yes | — |
| CustomerId | string | no | — |
| CreatedAt | DateTimeOffset | no | — |
| Completed | bool | no | — |

## Transactions

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | int | no | — |
| VoucherId | int | no | — |
| CustomerId | string | yes | — |
| CreatedAt | DateTime | no | CURRENT_TIMESTAMP |
| Status | int | no | — |
| Amount | decimal | no | — |
| ReservationId | string | no | — |

## VoucherTypes

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | int | no | — |
| ProviderId | int | no | — |
| Value | decimal | no | — |
| Name | string | yes | — |
| Currency | string | no | — |

## Vouchers

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | int | no | — |
| VoucherTypeId | int | no | — |
| Value | decimal | no | — |
| SerialNo | string | no | — |
| Secret | string | no | — |
| State | int | no | — |
| CreatedDate | DateTime | no | CURRENT_TIMESTAMP |
| RedeemedDate | DateTime | yes | — |
| ExpiryDate | DateTime | yes | — |
| ReservedAt | DateTime | yes | — |
| xmin | uint | no | — |
| OrderId | string | no | — |
| Pin | string | yes | — |
