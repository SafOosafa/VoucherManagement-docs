# Third-party provider configuration

## Overview

Third-party provider configurations are stored in the `ThirdPartyProviderConfigs` table in the Management database. The UI configures providers via the **Third-Party Stock** section.

---

## Configuration fields

| Field | Type | Purpose |
|-------|------|---------|
| `Id` | Guid | Primary key, used as `providerId` in API calls |
| `ProviderType` | string | Provider type identifier |
| `BaseUrl` | string | Base URL for the third-party API |
| `Config` | JSON | Provider-specific configuration (credentials, endpoints) |

**Note:** `providerId` = `ThirdPartyProviderConfigs.Id` — not the telecom `ProviderId`.

---

## Environment variables

Nested ASP.NET binding with double underscores:

```
ThirdPartyProviders__0__Config__Username
ThirdPartyProviders__0__Config__Password
```

---

## Related pages

- [Primo Wallet integration](primo-wallet-integration.md)
- [API conventions](../reference/api-conventions.md)
- [Management API reference](../reference/management-api.md)
