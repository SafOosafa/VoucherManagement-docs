# Load test reference runs

## Reference runs

| Run ID | Date | Scenario | Duration | Throughput |
|--------|------|----------|----------|------------|
| — | 2026-06 | 10k requests, no chaos | 60 min | ~146 ops/s |
| — | 2026-06 | 10k requests, with chaos | 60 min | ~89 ops/s |

---

## Test configuration

| Parameter | Value |
|-----------|-------|
| Concurrent clients | 50 |
| Ramp-up period | 30s |
| Think time | 100ms |
| Reservation rate | 10/s |
| Purchase rate | 8/s |

---

## Related pages

- [Stakeholder load test summary](README.md)
- [Load testing operations](../operations/load-testing-operations.md)
