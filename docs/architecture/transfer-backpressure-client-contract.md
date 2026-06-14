# Transfer backpressure (client contract)

## Concurrency limits

| Resource | Limit | Config key |
|----------|-------|------------|
| Max in-flight transfers | 1024 (default) | `MaxInFlightTransfers` |
| Reservation queue prefetch | Configurable | `RabbitMqOptions:PrefetchCount` |
| Saga concurrency | Per saga type | MassTransit concurrency limit |

---

## Client contract

Clients integrating with the External API or Orchestrator should expect:

- **202 Accepted** for async reservations — poll for completion
- **429 Too Many Requests** when rate limits are exceeded
- **503 Service Unavailable** during startup (health/ready returns 503)
- **409 Conflict** for duplicate idempotency keys

---

## Graceful degradation

When backpressure limits are reached:

1. New reservation requests are queued in RabbitMQ
2. Saga state machine holds in-flight purchases
3. Clients receive immediate 202 with `reservationId` for polling

---

## Related pages

- [Platform capabilities](README.md)
- [Production deployment](../operations/production-deployment.md)
