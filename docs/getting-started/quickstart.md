# 5-minute quickstart (integrators)

## Prerequisites

- Docker Compose stack running with `Mitf.Voucher.Stock.Api` on `http://localhost:5189`
- `curl` or PowerShell for HTTP requests
- JSON body payloads

---

## Step 1 — Health check (~30s)

Verify the Management API is ready:

```powershell
curl -sS http://localhost:5189/health/ready
```

Returns **200 OK** when migration and seed are complete. Returns **503** while seeding.

Liveness check:

```powershell
curl -sS http://localhost:5189/health
```

---

## Step 2 — Authenticate (~1 min)

Obtain a JWT token (when seed is enabled with dev login):

```powershell
$body = @{ username = "admin"; password = "admin" } | ConvertTo-Json
$token = curl -sS -X POST http://localhost:5189/api/authentication/authenticate `
  -H "Content-Type: application/json" -d $body | ConvertFrom-Json | Select -ExpandProperty token
```

---

## Step 3 — Browse the stock catalog (~1 min)

List providers:

```powershell
curl -sS http://localhost:5189/api/providers -H "Authorization: Bearer $token"
```

List main stock vouchers:

```powershell
curl -sS "http://localhost:5189/api/stock/main/vouchers?PageIndex=0&PageSize=10" `
  -H "Authorization: Bearer $token"
```

---

## Step 4 — Explore the API via Swagger (~1 min)

Open http://localhost:5189/swagger in your browser to explore all Management API endpoints interactively.

---

## Next steps

| You want to... | Go here |
|---|---|
| Understand the full service map | [Platform capabilities](../architecture/README.md) |
| See purchase / reservation flows | [Flow diagrams](../architecture/flow-diagrams.md) |
| Read per-service configuration | [Service reference index](../reference/) |
| Learn about API conventions | [API conventions](../reference/api-conventions.md) |
| Deploy to production | [Production deployment](../operations/production-deployment.md) |
