# Risk, compliance & finance

## Authentication & access control

| Surface | Mechanism | Risk control |
|---------|-----------|--------------|
| Management SPA + API | JWT Bearer + permission policies | Granular RBAC with `Permissions.*` policies |
| External / Internal / Orchestrator | `X-Api-Key` header | Key rotation, per-service isolation |
| Gateway | JWT Bearer for mobile clients | Token expiry, refresh token lifecycle |
| Wallet PIN | PBKDF2 with per-wallet salt | Brute-force lockout after 5 attempts |

## Data integrity

- **Idempotency guards** — Duplicate reservation keys, purchase requests, and ledger entries are rejected with idempotency mechanisms.
- **Immutable audit trail** — `UserActivities` table records all admin actions; reservation events are persisted to `ReservationEvents`.
- **Activity logging** — `GET /api/logs` returns structured entries with `user_id`, `action_type`, `target_entity_id`, `remarks`, and `timestamp`.

## Operational risk

| Risk | Mitigation |
|------|------------|
| Export insufficient stock | Atomic gRPC export with balance check before insert |
| Open custody transfer | Monthly block rule — `CUSTODY_TRANSFER_BLOCKED_MONTHLY_OPEN_TRANSFER` |
| Duplicate import secrets | `IMPORT_DUPLICATE_SECRET` rejection on CSV upload |
| Saga failure | MassTransit retry policy + state machine persistence |
| Queue spill | Reservation backpressure with configurable `MaxInFlightTransfers` |

---

## Related pages

- [System hardening](../security/README.md)
- [Platform capabilities](../architecture/README.md)
