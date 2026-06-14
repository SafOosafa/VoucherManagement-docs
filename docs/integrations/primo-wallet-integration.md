# Primo Wallet integration

## Overview

Management API integrates with Primo Wallet as a third-party stock provider. The integration supports:

- Product catalog sync
- Balance queries
- Bulk purchase / refill
- Order and transaction history

---

## Timestamp conventions

| Field / source | Convention | Display handling |
|----------------|------------|------------------|
| `requestDate`, `expires_at` | Libya wall clock (UTC+2) | Use as-is |
| `transaction_time` | Naive UTC | Add +2h for Libya display |
| Order `created_at` | Already Libya time | Pass through — do not add offset |

---

## SPA grouped transactions

The SPA merges related third-party transactions by extracted `#ref` reference:

- Utility: `thirdPartyTransactionMergeKey` in `third-party-display.util.ts`
- Merge key comes from description text, not stripped description alone

---

## Order deduplication

`MapOrders` dedupes by `transaction_id` and product catalog by `product_id`.

---

## Related docs

- [Management API reference](../reference/management-api.md) — third-party API prefixes
- [Reports API guide](../reference/reports-api-guide.md) — third-party endpoints
- [Primo Wallet timestamps](../reference/primo-wallet-timestamps.md)
