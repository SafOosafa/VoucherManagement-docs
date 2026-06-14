# Load testing — stakeholder summary

## Purpose

Load testing validates the Voucher Provider platform's capacity to handle concurrent reservation, purchase, and export operations under realistic conditions.

---

## Test scope

| Area | Tools | Metrics |
|------|-------|---------|
| API contracts | Newman | Response time, error rate |
| UI journeys | Playwright | Page load, CRUD completion |
| UI smoke | Maestro | Route availability |

---

## Capacity indicators (internal lab — not an SLA)

| Scenario | Throughput |
|----------|------------|
| Reservation + purchase (baseline) | ~146 ops/s |
| Reservation + purchase (with chaos) | ~89 ops/s |

---

## Related pages

- [Load test reference runs](load-test-reference-runs.md)
- [Load testing operations](../operations/load-testing-operations.md)
- [UI testing overview](../flows/ui-testing-overview.md)
