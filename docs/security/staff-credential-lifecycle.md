# Staff credential lifecycle

## JWT token lifecycle

```mermaid
flowchart LR
    subgraph Auth [Authentication]
        LOGIN[POST /api/authentication/authenticate] --> JWT[JWT issued]
        JWT --> ACCESS[Access token 60 min]
        JWT --> REFRESH[Refresh token 7 days]
    end
    subgraph Renewal [Token Renewal]
        REFRESH --> REFRESHAPI[POST /api/authentication/refresh]
        REFRESHAPI --> JWT2[New JWT pair]
    end
    subgraph End [Termination]
        EXPIRY[Token expiry] --> LOGOUT[Logout]
        LOGOUT --> DELETE[Refresh token revoked]
    end
```

---

## Password policy

| Setting | Value |
|---------|-------|
| Min length | 8 characters |
| EasyPass bypass | Dev only (`Auth:AllowEasyPass`) |
| Password reset | Admin-initiated via Management API |

---

## Permission management

Admin users manage permissions via the SPA (`/api/users/*` + `/api/permissions/*`). Changes take effect on next request (JWT must be re-issued).

---

## Related pages

- [System hardening](README.md)
- [KYC flow, validation & portal approval](../architecture/kyc-flow-validation-and-portal-approval.md)
