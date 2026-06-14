# Management database schema

Source migration: `voucher.management/Mitf.Voucher.Infrastructure/Migrations/20260606184943_PostgresInitial.cs`

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
| OfferId | int | no | — |
| Name | string | no | — |
| OfferCode | string | no | — |
| ProviderId | int | no | — |
| ReplyPrefixCode | string | no | — |
| Price | decimal | no | — |

## BundleVoucherArchives

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
| OrderId | int | no | — |
| ReservedAt | DateTime | yes | — |

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

## CustodyTransfers

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | int | no | — |
| SourceUserId | int | no | — |
| FromBranchId | int | no | — |
| ToBranchId | int | no | — |
| VoucherTypeId | int | no | — |
| RequestedCount | int | no | — |
| ExportRequestId | int | yes | — |
| ExportBatchId | Guid | yes | — |
| Remarks | string | yes | — |
| Status | int | no | — |
| CreatedAt | DateTime | no | — |

## ExportBundleRequests

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | int | no | — |
| ForeignStockId | int | no | — |
| BundleDefinitionId | int | no | — |
| BundleCount | int | no | — |
| UserId | int | no | — |
| Notes | string | yes | — |
| Status | int | no | — |

## ExportOrders

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | int | no | — |
| VoucherCount | int | no | — |
| VoucherTypeId | int | no | — |
| UserId | int | no | — |
| ForeignStockId | int | no | — |
| TransferDate | DateTime | no | CURRENT_TIMESTAMP |
| Status | int | no | — |
| Notes | string | yes | — |

## ForeignStocks

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | int | no | — |
| StockName | string | no | — |
| ConnectionString | string | no | — |
| EncryptionKey | string | no | — |
| ApiUrl | string | yes | — |

## ImportOrders

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | int | no | — |
| ImportRequestId | int | no | — |
| VoucherTypeId | int | yes | — |
| BundleDefinitionId | int | yes | — |
| OrderId | string | no | — |
| VoucherCount | int | no | — |
| MinSerialNo | string | yes | — |
| MaxSerialNo | string | yes | — |

## ImportRequests

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | int | no | — |
| OrderId | string | no | — |
| ProviderId | int | no | — |
| UserId | int | no | — |
| State | int | no | — |
| Notes | string | yes | — |
| CreatedAt | DateTime | no | — |
| OrderDate | DateTime | yes | — |

## InternalStockLowAlerts

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | int | no | — |
| RuleId | int | no | — |
| CurrentCountAtTrigger | int | no | — |
| ThresholdCount | int | no | — |
| Status | int | no | — |
| TriggeredAt | DateTime | no | — |
| LastEvaluatedAt | DateTime | no | — |
| LastObservedCount | int | no | — |
| IsRecovered | bool | no | — |
| AcknowledgedAt | DateTime | yes | — |

## InternalStockThresholdRules

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | int | no | — |
| ForeignStockId | int | no | — |
| ProviderId | int | no | — |
| VoucherTypeId | int | no | — |
| BaselineCount | int | no | — |
| ThresholdPercent | int | no | — |
| IsDisabled | bool | no | — |
| CreatedAt | DateTime | no | — |
| UpdatedAt | DateTime | no | — |

## InternationalStockArchives

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | long | no | — |
| VoucherTypeId | int | no | — |
| ImportRequestId | int | no | — |
| Value | decimal | no | — |
| SerialNo | string | no | — |
| Secret | string | no | — |
| CreatedDate | DateTime | no | — |
| State | int | no | — |
| OrderId | string | yes | — |
| ExportRequestId | int | yes | — |

## InternationalVouchers

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | long | no | — |
| VoucherTypeId | int | no | — |
| ImportRequestId | int | no | — |
| Value | decimal | no | — |
| SerialNo | string | no | — |
| Secret | string | no | — |
| CreatedDate | DateTime | no | — |
| State | int | no | — |
| OrderId | string | yes | — |

## MainStockArchive

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | int | no | — |
| VoucherTypeId | int | no | — |
| Value | decimal | no | — |
| SerialNo | string | no | — |
| Secret | string | no | — |
| CreatedDate | DateTime | no | CURRENT_TIMESTAMP |
| OrderId | string | no | — |
| Pin | string | yes | — |
| ExportRequestId | int | yes | — |

## Providers

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | int | no | — |
| ProviderId | int | no | — |
| Name | string | no | — |
| Description | string | yes | — |
| Region | string | yes | — |
| Brand | string | yes | — |
| CreatedAt | DateTime | no | — |

## RefreshTokens

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | int | no | — |
| Token | string | no | — |
| ExpiryDate | DateTime | no | — |

## StockRefillRequests

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | string | no | — |
| ProviderId | int | no | — |
| OrderId | string | no | — |
| UserId | int | no | — |
| Status | string | no | — |
| RequestJson | string | no | — |
| ResponseJson | string | yes | — |
| ErrorMessage | string | yes | — |
| CreatedAt | DateTime | no | — |

## StockRefillTemplates

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | int | no | — |
| Name | string | no | — |
| ProviderId | int | no | — |
| RequestJson | string | no | — |
| CreatedAt | DateTime | no | — |

## SystemConfigurationRules

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | int | no | — |
| ForeignStockId | int | no | — |
| EntityType | int | no | — |
| EntityId | int | no | — |

## ThirdPartyProviderConfigs

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | int | no | — |
| Name | string | no | — |
| ProviderType | string | no | — |
| BaseUrl | string | no | — |
| ConfigurationJson | string | no | — |
| IsActive | bool | no | — |
| LastSuccessAt | DateTime | yes | — |
| LastFailureAt | DateTime | yes | — |
| LastErrorSummary | string | yes | — |
| ConsecutiveFailures | int | no | — |
| CircuitStateSnapshot | string | yes | — |
| HealthCheckDisabled | bool | no | — |
| CreatedAt | DateTime | no | — |

## UploadedFiles

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | int | no | — |
| FileHash | string | no | — |
| FileName | string | no | — |

## UserActivities

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | int | no | — |
| UserId | int | no | — |
| Action | string | no | — |
| Module | string | no | — |
| Description | string | no | — |
| Details | string | yes | — |
| IpAddress | string | yes | — |

## Users

**Seed data:** Has `InsertData` seed row(s) in migration (see source file).

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | int | no | — |
| Name | string | no | — |
| Username | string | no | — |
| Email | string | yes | — |
| PasswordHash | string | no | — |
| Role | int | no | — |
| State | int | no | — |
| Permissions | int | no | — |
| MfaSecret | string | yes | — |
| IsMfaEnabled | bool | no | — |

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
| ImportRequestId | int | no | — |
