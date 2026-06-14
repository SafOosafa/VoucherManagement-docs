# Configuration reference

## Common configuration sections

All APIs share these configuration patterns via the `Mitf.Observability` NuGet package:

| Section | Purpose |
|---------|---------|
| `ConfigSource` | Config source (appsettings or Consul) |
| `DbOptions` / `ConnectionStrings` | Database connection |
| `InternalLoggerOptions` | Structured API logging |
| `Observability` | OpenTelemetry / metrics |
| `swagger` | Swashbuckle metadata |

---

## Per-service configuration

| Service | Configuration doc |
|---------|-------------------|
| Management API | [management-api.md](management-api.md#configuration) |
| External API | [external-api.md](external-api.md#configuration) |
| Internal API | [internal-api.md](internal-api.md#configuration) |
| Gateway | [gateway.md](gateway.md#configuration) |
| Purchase orchestrator | [purchase-orchestrator.md](purchase-orchestrator.md#configuration) |
| SPA | [vouchers-stock-spa.md](vouchers-stock-spa.md#configuration) |

---

## Environment variable binding

Nested ASP.NET configuration uses **double underscores** between section and property:

```
Section__SubSection__PropertyName
```

Example:

```
ThirdPartyProviders__0__Config__Username
ManagementDataSeed__Enabled=true
RabbitMqOptions__RabbitMqVirtualHost=voucher
```

---

## Related pages

- [API reference](api.md)
- [Service reference index](.)
