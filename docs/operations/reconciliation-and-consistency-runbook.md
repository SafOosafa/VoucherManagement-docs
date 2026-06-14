# Reconciliation & consistency runbook

## Overview

The Voucher Provider platform maintains consistency across services through:

- **Idempotency guards** — Duplicate operations are rejected at the API and database level
- **Saga state persistence** — Multi-step purchase orchestrations are durable across restarts
- **Event logging** — All reservation events are persisted for audit trail

---

## Consistency checks

| Check | Frequency | Method |
|-------|-----------|--------|
| Reservation consistency | Real-time | `ReservationIdempotencyRecords` table |
| Purchase idempotency | Real-time | `PurchaseIdempotencyRecords` table |
| Export integrity | Per operation | gRPC response validation |
| Stock balance reconciliation | Periodic | Management API report queries |

---

## Common issues

| Issue | Symptom | Resolution |
|-------|---------|------------|
| Duplicate reservation | 409 Conflict | Check `IdempotencyKey` |
| Stale saga state | Purchase stuck in progress | Restart orchestrator with MassTransit recovery |
| CORS error | Browser request fails | Verify `OriginsOption` includes exact origin |
| Seed timeout | 503 on `/health/ready` | Wait for seed to complete (~120s cold start) |

---

## Related pages

- [Production deployment](production-deployment.md)
- [Logging](logging.md)
- [Financial operations & reconciliation](../reconciliation/README.md)
