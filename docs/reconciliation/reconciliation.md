# Reconciliation job

## Purpose

Compare stock levels between:

- Central Management API database
- Per-bank Internal API databases (via gRPC)
- Third-party provider balances (via HTTPS)

---

## Reconciliation steps

1. **Export** — Query stock counts from Management API
2. **Bank alignment** — Query stock counts from each Internal API via gRPC
3. **Third-party check** — Query balance from third-party providers
4. **Report** — Generate exception report

---

## Exception types

| Type | Description |
|------|-------------|
| `MissingInBank` | Voucher expected in bank but not found |
| `MissingInCentral` | Voucher expected in central stock but not found |
| `CountMismatch` | Stock count differs between central and bank |

---

## Related pages

- [Financial operations & reconciliation](README.md)
- [Flow diagrams](../architecture/flow-diagrams.md)
- [Operations runbook](../operations/reconciliation-and-consistency-runbook.md)
