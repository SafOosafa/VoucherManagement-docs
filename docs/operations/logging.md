# Observability and logging

All APIs use [Mitf.Observability](https://www.nuget.org/packages/Mitf.Observability) with Consul, Serilog sinks, and OpenTelemetry disabled by default.

---

## Local Docker stack

```powershell
docker compose --profile observability up -d
docker compose up --build mitf.voucher.stock.api
```

| Service | Host URL |
|---------|----------|
| Consul UI | http://localhost:28520 |
| Grafana | http://localhost:4021 (admin / admin) |
| Prometheus | http://localhost:29190 |
| Loki | http://localhost:23120 |
| OTEL gRPC | localhost:24417 |

---

## Enable on APIs

Set environment variables (or Consul KV) when running with the observability profile:

```env
OBSERVABILITY_ENABLED=true
OBSERVABILITY_COLLECTOR_URL=http://otel-collector:4317
CONFIG_SOURCE=consul
CONSUL_ADDRESS=http://consul:8500
INTERNAL_LOGGER_ENABLED=true
INTERNAL_LOGGER_LOG_TYPE=Loki
INTERNAL_LOGGER_CONNECTION_STRING=http://loki:3100
```

Gateway and Purchase Orchestrator keep `InternalLoggerOptions__Enabled=false` in compose (legacy `LogOptions` / semi-logger).

---

## Verification

```powershell
curl http://localhost:5189/health
curl http://localhost:5189/metrics   # requires Observability__Enabled=true
curl http://localhost:29190/api/v1/targets
```

---

## Tests

```powershell
dotnet test voucher.management/Mitf.Voucher.Stock.Management.Tests/ --filter ObservabilityRegistrationTests
```

---

## Related pages

- [Production deployment](production-deployment.md)
- [Local compose and QA environment](local-compose-and-qa.md)
