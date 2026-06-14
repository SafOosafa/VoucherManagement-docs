# Primo Wallet timestamp conventions

Management API client for Primo Wallet (`voucher.management/Clients/PrimoWallet/`, centralized in `PrimoWalletTime.cs`). QA validating third-party orders and grouped transactions must apply these rules.

---

## Field conventions

| Field / source | Convention | Display handling |
|----------------|------------|------------------|
| `requestDate`, `expires_at` | **Libya wall clock (UTC+2)** | Use as-is |
| `transaction_time` | **Naive UTC** | Add +2h for Libya display |
| Order `created_at` | **Already Libya time** | Pass through — do not add offset |

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

- [management-api.md](management-api.md) — third-party API prefixes
- [reports-api-guide.md](reports-api-guide.md) — third-party endpoints
- `voucher.management/docs/WEB-DEVELOPER-API-INTEGRATION.md` — full integration examples
