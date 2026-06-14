# Internal database schema

Source migration: `voucher.internal/Mitf.Voucher.Internal.Infrastructure/Migrations/20260606184534_PostgresInitial.cs`

Primary keys use PostgreSQL identity / serial unless noted. `—` = no server default.

## BundleComponents

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | int | no | — |
| BundleDefinitionId | int | no | — |
| Type | int | no | — |
| Amount | decimal | no | — |

## BundleDefinitions

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | int | no | — |
| ProviderId | int | no | — |
| OfferId | int | no | — |
| ManagementBundleDefinitionId | int | no | — |
| Name | string | no | — |
| OfferCode | string | no | — |
| ReplyPrefixCode | string | no | — |
| Price | decimal | no | — |

## BundleTransactions

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | long | no | — |
| ProviderId | int | no | — |
| BundleVoucherId | long | no | — |
| CustomerId | string | yes | — |
| FinFileNO | int | no | — |
| ChannelId | int | no | — |
| TraceId | string | no | — |
| AccountNumber | string | no | — |
| CreatedAt | DateTime | no | — |
| Status | int | no | — |
| Amount | decimal | no | — |
| ReservationId | string | no | — |

## BundleVouchers

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | long | no | — |
| ProviderId | int | no | — |
| BundleDefinitionId | int | no | — |
| SerialNumber | string | no | — |
| Secret | string | no | — |
| Value | decimal | no | — |
| ExpiryDate | DateOnly | yes | — |
| State | int | no | — |
| ReservedAt | DateTime | yes | — |

## EnabledEntities

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | int | no | — |
| EntityType | int | no | — |

## Providers

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | int | no | — |
| ProviderId | int | no | — |
| Name | string | no | — |
| Description | string | no | — |
| Brand | string | yes | — |
| Region | string | yes | — |

## Transactions

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | int | no | — |
| ProviderId | int | no | — |
| VoucherId | int | no | — |
| CustomerId | string | yes | — |
| FinFileNO | int | no | — |
| ChannelId | int | no | — |
| TraceId | string | no | — |
| AccountNumber | string | no | — |
| CreatedAt | DateTime | no | — |
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
| ManagementVoucherTypeId | int | yes | — |

## Vouchers

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | int | no | — |
| VoucherTypeId | int | no | — |
| Value | decimal | no | — |
| SerialNo | string | no | — |
| Secret | string | no | — |
| State | int | no | — |
| CreatedDate | DateTime | no | — |
| RedeemedDate | DateTime | yes | — |
| ExpiryDate | DateTime | yes | — |
| ReservedAt | DateTime | yes | — |
| xmin | uint | no | — |
| OrderId | string | no | — |
| Pin | string | yes | — |
| ExportRequestId | int | no | — |

## VouchersArchive

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | int | no | — |
| OriginalVoucherId | int | no | — |
| VoucherTypeId | int | no | — |
| Value | decimal | no | — |
| SerialNo | string | no | — |
| Secret | string | no | — |
| State | int | no | — |
| CreatedDate | DateTime | no | — |
| RedeemedDate | DateTime | yes | — |
| ExpiryDate | DateTime | yes | — |
| ReservedAt | DateTime | yes | — |
| OrderId | string | no | — |
| Pin | string | yes | — |
| ExportRequestId | int | no | — |
| ReclaimBatchId | Guid | yes | — |
| ArchivedAt | DateTime | no | — |
