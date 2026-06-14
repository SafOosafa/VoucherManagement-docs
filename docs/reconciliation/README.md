# Financial operations & reconciliation

## Glossary

| Term | Definition |
|------|------------|
| **Voucher stock** | Digital voucher inventory (serial numbers + secrets) |
| **Central stock** | Main stock held in Management API database |
| **Bank stock** | Per-bank stock held in Internal API databases |
| **Export** | Transfer of vouchers from central stock to bank stock |
| **Reservation** | Async lock of a voucher for purchase |
| **Purchase** | Confirmed transfer of a voucher to an end customer |
| **Reconciliation** | Comparison of stock levels across services |

---

## Stock types and flow

| Type | Source | Destination | Mechanism |
|------|--------|-------------|-----------|
| Central import | Telecom provider | Management DB | CSV upload |
| Bank export | Management DB | Internal API DB | gRPC BulkInsertVouchers |
| Branch transfer | Internal API A | Internal API B | gRPC AcquireTransfer |
| Third-party refill | Primo Wallet | Management DB | HTTPS + job queue |
| Purchase | Internal / External | Customer | Reserve → Confirm |

---

## Transaction lifecycle

| Status | Description |
|--------|-------------|
| `Pending` | Reservation created, voucher locked |
| `Completed` | Purchase confirmed, voucher delivered |
| `Failed` | Insufficient stock, timeout, or error |
| `Cancelled` | Reservation released before confirmation |
| `Reversed` | Completed transaction manually reversed |

---

## Exception handling

| Exception type | Meaning | Action |
|----------------|---------|--------|
| `IMPORT_DUPLICATE_SECRET` | Duplicate voucher secret in import CSV | Remove duplicate and retry |
| `EXPORT_INSUFFICIENT_STOCK` | Not enough stock for export | Check stock levels |
| `CUSTODY_TRANSFER_BLOCKED_MONTHLY_OPEN_TRANSFER` | Open custody transfer exists | Close open transfer first |
| `ORDER_ID_EDIT_NOT_ALLOWED_STATE` | Order edit blocked by state | Only edit inactive requests |

---

## Related pages

- [Reconciliation job](reconciliation.md)
- [Production deployment](../operations/production-deployment.md)
- [Flow diagrams](../architecture/flow-diagrams.md)
