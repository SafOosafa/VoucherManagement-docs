# Transaction flows & ledger examples

## Purchase lifecycle

| Step | Status | Description |
|------|--------|-------------|
| 1 | `Pending` | Reservation created |
| 2 | `Completed` | Voucher locked, purchase confirmed |
| 3 | `Failed` | Insufficient stock or timeout |
| 4 | `Reversed` | Manual reversal of completed transaction |

---

## Transaction types

| Type | Flow | Participants |
|------|------|-------------|
| P2P Transfer | Reserve → Confirm | Gateway → Internal |
| Fund Wallet / Top-Up | Reserve → Purchase | Bank → External |
| Merchant Payment | Buy voucher → Confirm | Client → Orchestrator → Internal + Bank |
| Bundle Purchase | Reserve bundles → Buy → Confirm bundles | Orchestrator → Internal + External |
| International Purchase | Reserve international → Buy → Confirm | Orchestrator → Internal + Bank |

---

## Idempotency key usage

| Operation | Idempotency key location |
|-----------|--------------------------|
| Reservation | `IdempotencyKey` in request body |
| Purchase | `RequestId` in orchestration |
| Export | gRPC operation is inherently idempotent |

---

## Related pages

- [Flow diagrams](flow-diagrams.md)
- [Platform capabilities](README.md)
- [API conventions](../reference/api-conventions.md)
