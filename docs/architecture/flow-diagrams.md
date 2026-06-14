# Flow diagrams

End-to-end flows for QA and integrators. Pair with [service reference index](../reference/) for endpoint tables.

---

## 1. Purchase / reservation flow

Three related paths: Gateway BFF, External async reservations, and Orchestrator sagas.

### 1A. Gateway BFF (synchronous)

```mermaid
sequenceDiagram
    participant Client as Mobile client
    participant GW as Gateway :5089
    participant Stock as StockProviderLink
    Client->>GW: POST /api/voucher/reserve (JWT)
    GW->>Stock: POST /api/reserve
    Stock-->>GW: reservationId
    Client->>GW: POST /api/voucher/handel-purchase
    GW->>Stock: POST /api/handle-purchase
    GW-->>Client: PurchaseResult
```

### 1B. External API (async via RabbitMQ)

```mermaid
sequenceDiagram
    participant Client as Bank channel
    participant Ext as External :5252
    participant RMQ as RabbitMQ
    participant RC as ReservationConsumer
    participant PG as External DB

    Client->>Ext: POST /api/reserve
    Ext->>RMQ: ReservationRequest
    Ext-->>Client: reservationId (submitted)
    RMQ->>RC: reservation-requests queue
    RC->>PG: lock voucher, pending txn
    Client->>Ext: POST /api/handle-purchase
    Ext->>PG: confirm/cancel
```

### 1C. Purchase orchestrator (bundle saga)

Requires compose profile `orchestrator`.

```mermaid
sequenceDiagram
    participant Client as Purchase client
    participant Orch as Orchestrator :5260
    participant Int as Internal :5238
    participant Bank as External :5252

    Client->>Orch: POST /api/purchase-requests
    Orch->>Int: POST /api/bundles/reserve
    Orch->>Bank: POST /api/Transaction/BuyVoucher
    Orch->>Int: POST /api/bundles/confirm
    Orch-->>Client: purchase result
```

**Saga states:** Initial → AwaitingReservation → AwaitingBankTransaction → AwaitingConfirmation → AwaitingArchiving → Finalized

---

## 2. Stock management flow

```mermaid
flowchart LR
    SPA[vouchers-stock :4200] -->|JWT| API[Management :5189]
    API --> PG[(Management DB)]
    API -->|gRPC| BANKS[Internal x6]
    API -->|HTTPS| TP[Third-party]
```

**Startup:** Migrations/seed run in background. `GET /health/ready` returns **503** until seed completes.

**Key operations:**

| Operation | Mechanism |
|-----------|-----------|
| Import vouchers | Local MainStock DB only |
| Export to bank | Lock local stock → gRPC BulkInsertVouchers |
| Branch transfer | gRPC AcquireTransferVouchers / ReleaseTransferVouchers |
| Reports on bank stock | gRPC read RPCs |
| Third-party refill | HTTPS to Primo + local job queue |

SPA routes vs API paths: [Reports API guide](../reference/reports-api-guide.md)

---

## 3. Third-party stock integration

```mermaid
sequenceDiagram
    participant SPA as Management SPA
    participant API as Stock API
    participant Up as Primo Wallet HTTPS

    SPA->>API: GET /api/stock/thirdparty/config
    SPA->>API: GET /api/stock/thirdparty/balance?providerId=
    SPA->>API: POST /api/stock/refill
    API->>Up: bulk purchase / catalog
    SPA->>API: GET /api/stock/refill/{jobId}
```

`providerId` = **ThirdPartyProviderConfigs.Id** — see [API conventions](../reference/api-conventions.md).

Timestamp rules: [Primo Wallet timestamps](../reference/primo-wallet-timestamps.md)

---

## 4. Remote foreign stock gRPC flow

**Proto:** `grpc/stock_remote_v1.proto`

```mermaid
sequenceDiagram
    participant MGMT as Management API
    participant GW as IRemoteStockGrpcGateway
    participant BANK as Internal gRPC :8081
    participant DB as Bank Postgres

    MGMT->>GW: BulkInsertVouchers (export)
    GW->>BANK: gRPC
    BANK->>DB: insert stock
    MGMT->>GW: GetActiveVoucherCount (report)
    GW->>BANK: gRPC read
```

---

## 5. RabbitMQ / MassTransit message flows

| Service | MassTransit |
|---------|-------------|
| External | Reservation consumers |
| Orchestrator | Bundle + international sagas |
| Management, Internal, Gateway | None |

**Broker:** RabbitMQ vhost `voucher`, UI http://localhost:15672

**External queues:** `reservation-requests`, `reservation-event-logger`

---

## QA journey flows

| Tag | Command | Flow |
|-----|---------|------|
| `@smoke` | `npm run e2e -- --grep @smoke` | Critical path smoke |
| `@journey` | `npm run e2e:journey` | Import → export → bundles → international (Demo Libyana 13201, Al Madar 13202) |
| `@internal` | `npm run e2e:internal` | Internal API Jumhoria :5238 |

Details: [UI testing overview](../flows/ui-testing-overview.md)
