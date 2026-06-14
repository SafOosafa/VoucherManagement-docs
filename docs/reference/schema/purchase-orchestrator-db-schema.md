# Purchase Orchestrator database schema

Source migration: `voucher.purchaseorchestrator/Voucher.PurchaseOrchestrator.Infrastructure/Migrations/20260606184636_PostgresInitial.cs`

Primary keys use PostgreSQL identity / serial unless noted. `—` = no server default.

## BundlePurchaseArchives

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| CorrelationId | Guid | no | — |
| Outcome | int | no | — |
| OutcomeMessage | string | yes | — |
| UserId | string | no | — |
| ProviderId | int | no | — |
| OfferId | int | no | — |
| Amount | decimal | no | — |
| ConfirmationCode | string | yes | — |
| SerialNumber | string | yes | — |
| EncryptedSecret | string | yes | — |
| BankTransactionSuccess | bool | yes | — |
| FinFileNO | int | no | — |
| ChannelId | int | no | — |
| AccountNumber | string | no | — |
| RequestId | string | no | — |

## BundleSagaState

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| CorrelationId | Guid | no | — |
| CurrentState | string | no | — |
| CreationTime | DateTime | no | — |
| SeqNO | int | no | — |
| UserId | string | no | — |
| ClassificationId | int | no | — |
| ProviderId | int | no | — |
| OfferId | int | no | — |
| Amount | decimal | no | — |
| ProductId | int | no | — |
| AccountType | int | no | — |
| ConfirmationCode | string | yes | — |
| SerialNumber | string | yes | — |
| EncryptedSecret | string | yes | — |
| FinFileNO | int | no | — |
| ChannelId | int | no | — |
| AccountNumber | string | no | — |
| RequestId | string | no | — |
| BankTransactionSuccess | bool | yes | — |
| BankState | string | yes | — |
| BankFailureReason | string | yes | — |
| Outcome | int | no | — |

## InternationalPurchaseArchives

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| CorrelationId | Guid | no | — |
| Outcome | int | no | — |
| OutcomeMessage | string | yes | — |
| UserId | string | no | — |
| ProviderId | int | no | — |
| VoucherTypeId | int | no | — |
| Amount | int | no | — |
| ConfirmationCode | string | yes | — |
| SerialNumber | string | yes | — |
| EncryptedSecret | string | yes | — |
| BankTransactionSuccess | bool | yes | — |
| FinFileNO | int | no | — |
| ChannelId | int | no | — |
| AccountNumber | string | no | — |
| RequestId | string | no | — |

## InternationalSagaState

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| CorrelationId | Guid | no | — |
| CurrentState | string | no | — |
| CreationTime | DateTime | no | — |
| SeqNO | int | no | — |
| UserId | string | no | — |
| ClassificationId | int | no | — |
| ProviderId | int | no | — |
| VoucherTypeId | int | no | — |
| Amount | int | no | — |
| ProductId | int | no | — |
| AccountType | int | no | — |
| ConfirmationCode | string | yes | — |
| SerialNumber | string | yes | — |
| EncryptedSecret | string | yes | — |
| FinFileNO | int | no | — |
| ChannelId | int | no | — |
| AccountNumber | string | no | — |
| RequestId | string | no | — |
| BankTransactionSuccess | bool | yes | — |
| BankState | string | yes | — |
| BankFailureReason | string | yes | — |
| Outcome | int | no | — |
| FailureReason | string | yes | — |

## Logs

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | int | no | — |
| Timestamp | DateTime | no | — |
| RequestName | string | no | — |
| RequestContent | string | yes | — |
| ResponseContent | string | yes | — |
| DurationMs | int | no | — |
| ExceptionMessage | string | yes | — |
| CorrelationId | string | yes | — |
| AppId | string | yes | — |

## PurchaseIdempotencyRecords

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | long | no | — |
| ChannelId | int | no | — |
| RequestId | string | no | — |
| SagaCorrelationId | Guid | no | — |
| CreatedAt | DateTimeOffset | no | — |
| CompletedAt | DateTimeOffset | yes | — |

## StatisticDoc

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Key | string | no | — |

## TransactionSequences

| Column | CLR type | Nullable | Default |
|--------|----------|----------|---------|
| Id | int | no | — |
| CorrelationId | string | no | — |
