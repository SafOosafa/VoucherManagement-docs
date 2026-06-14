# Onboarding channel hardening

## API surface security

| Channel | Auth mechanism | Hardening |
|---------|---------------|-----------|
| Management API | JWT Bearer + permission policies | Granular RBAC; CORS exact origin |
| External API | `X-Api-Key` | Key rotation; network segmentation |
| Internal APIs | `X-Api-Key` | Per-bank key isolation |
| Gateway | JWT Bearer | Token expiry; refresh lifecycle |
| Orchestrator | `X-Api-Key` | Key rotation; idempotency guards |

---

## CORS configuration

The Management API requires exact origin matching (scheme + host + port):

```
OriginsOption__AllowedOrigins__0 = http://localhost:4200
OriginsOption__AllowedOrigins__1 = http://127.0.0.1:4200
```

**Symptom:** Swagger/Postman works but browser shows CORS error → missing or wrong origin entry.

---

## Network segmentation

| Tier | Contains | Access |
|------|----------|--------|
| DMZ | Gateway, External API | Public-facing |
| Application | Management, Internal APIs, Orchestrator | Internal network only |
| Backend | PostgreSQL, Redis, RabbitMQ | Application tier only |

---

## Related pages

- [System hardening](README.md)
- [Staff credential lifecycle](staff-credential-lifecycle.md)
- [Production deployment](../operations/production-deployment.md)
