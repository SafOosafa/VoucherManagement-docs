# KYC flow, validation & portal approval

## Authentication flows

```mermaid
flowchart TB
    subgraph management [Management API :5189]
        JWT[JWT Bearer Auth]
        PERM[Permission Policies]
    end
    subgraph gateway [Gateway :5089]
        JWT2[JWT Bearer Auth]
    end
    subgraph apikey [API-key services]
        EXT[External :5252]
        INT[Internal :5238-5243]
        ORCH[Orchestrator :5260]
    end

    ManagementSPA -->|login| JWT
    JWT -->|validate| PERM
    Mobile -->|login| JWT2
    Bank -->|X-Api-Key| EXT
```

---

## Permission model (Management API)

| Permission | Scope |
|------------|-------|
| `Permissions.Users.Read` | View user list |
| `Permissions.Users.Write` | Create / edit users |
| `Permissions.Stock.Read` | View stock inventory |
| `Permissions.Stock.Write` | Import / export / transfer |
| `Permissions.Reports.Read` | View reports |
| `Permissions.Reports.Export` | Export report CSV |
| `Permissions.Configuration.Read` | View configuration |
| `Permissions.Configuration.Write` | Edit configuration |

---

## Related pages

- [System hardening](../security/README.md)
- [Staff credential lifecycle](../security/staff-credential-lifecycle.md)
- [API conventions](../reference/api-conventions.md)
