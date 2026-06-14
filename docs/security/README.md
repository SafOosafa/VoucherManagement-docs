# System hardening and security

## 1. API key authentication

When enabled, every request (REST + gRPC) must include a valid API key or is rejected with **401 Unauthorized**.

**Applies to:** Users (5003), Ledger (5001), Wallets (5002), Transactions (5004)

**Bypass:** `/health`, `/health/ready`

**Header options:**

| Header | Format |
|--------|--------|
| `X-Api-Key` | `<key>` |
| `Authorization` | `ApiKey <key>` |
| gRPC metadata | `x-api-key` |

**Configuration keys:**

| Key | Purpose |
|-----|---------|
| `Auth:ApiKey` | The expected API key value |
| `Auth:RequireApiKey` | Enable/disable requirement (default: true) |

---

## 2. JWT Bearer authentication

**Applies to:** Management API (:5189), Gateway (:5089)

| Setting | Default |
|---------|---------|
| Token expiry | 60 minutes |
| Refresh token expiry | 7 days |
| Algorithm | HMAC-SHA256 |

---

## 3. Permission policies

Granular RBAC via `Permissions.*` policies:

- `Permissions.Users.Read` / `Write`
- `Permissions.Stock.Read` / `Write`
- `Permissions.Reports.Read` / `Export`
- `Permissions.Configuration.Read` / `Write`

---

## 4. Logging and sensitive data

Request logging config with redacted headers:

| Redacted header | Redacted body property |
|-----------------|----------------------|
| `Authorization` | `password` |
| `Cookie` | `apiKey` |
| `X-Api-Key` | `token` |

Body logging defaults to `false`. ExcludePaths for health/metrics.

---

## 5. Operational recommendations

| Area | Recommendation |
|------|----------------|
| **TLS** | HTTPS for all public-facing endpoints |
| **Secrets** | Rotate API keys and JWT signing keys regularly |
| **Health endpoints** | Do not expose `/health` or `/metrics` to public internet |
| **Swagger** | Disable Swagger UI in production |
| **Network** | Segment APIs into DMZ / Application / Backend tiers |

---

## Related pages

- [Onboarding channel hardening](onboarding-channel-hardening.md)
- [Staff credential lifecycle](staff-credential-lifecycle.md)
- [Risk, compliance & finance](../stakeholders/risk-compliance-and-finance.md)
