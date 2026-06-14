# Masarat segments — service personas

The Voucher Provider platform serves three primary consumer segments, each with distinct API surfaces and integration patterns.

---

## Segment mapping

| Segment | API surface | Auth | Typical client |
|---------|-------------|------|----------------|
| **Management (admin)** | Management API :5189 | JWT + permission policies | Admin SPA (:4200) |
| **Bank channel** | External API :5252 | API key | Bank backend systems |
| **Mobile / retail** | Gateway :5089 | JWT (mobile user) | Mobile apps |
| **Internal bank stock** | Internal APIs :5238–5243 | API key | Per-bank services |
| **Purchase orchestration** | Orchestrator :5260 | API key | Saga coordination |

---

## Interaction patterns

```mermaid
flowchart LR
    subgraph retail [Retail / Mobile]
        Mobile[Mobile App] -->|JWT| GW[Gateway :5089]
        GW -->|HTTP| INT[Internal :5238]
    end
    subgraph business [Bank Channel]
        Bank[Bank Systems] -->|API Key| EXT[External :5252]
    end
    subgraph foreign [Management]
        SPA[Admin SPA :4200] -->|JWT| MGMT[Management :5189]
        MGMT -->|gRPC| INT
        MGMT -->|HTTPS| TP[Third Party]
    end
```

---

## Related pages

- [Platform capabilities](../architecture/README.md)
- [Service reference index](../reference/)
