# Domain events

## Event catalog

| Event | Source | Consumer(s) | Mechanism |
|-------|--------|-------------|-----------|
| `ReservationRequest` | External API | ReservationConsumer (External) | MassTransit / RabbitMQ |
| `ReservationConfirmed` | External API | ReservationEventLogger | MassTransit / RabbitMQ |
| `ReservationCancelled` | External API | ReservationEventLogger | MassTransit / RabbitMQ |
| `BundlePurchaseStarted` | Orchestrator | Saga state machine | MassTransit |
| `InternationalPurchaseStarted` | Orchestrator | Saga state machine | MassTransit |
| `PurchaseCompleted` | Orchestrator | Archives | MassTransit |

---

## Event flow

```mermaid
flowchart LR
    subgraph external [External API]
        RR[ReservationRequest] --> RMQ
        RC[ReservationConfirmed] --> RMQ
        RCL[ReservationCancelled] --> RMQ
    end
    subgraph orch [Orchestrator]
        BP[BundlePurchaseStarted] --> RMQ
        IP[InternationalPurchaseStarted] --> RMQ
    end
    RMQ[(RabbitMQ)] --> RES[ReservationConsumer]
    RMQ --> REL[ReservationEventLogger]
    RMQ --> SAGA[Bundle Saga]
    RMQ --> ISAGA[International Saga]
```

---

## Related pages

- [Flow diagrams](flow-diagrams.md)
- [Platform capabilities](README.md)
