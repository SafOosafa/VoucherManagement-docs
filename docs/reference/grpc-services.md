# gRPC services

## StockRemoteBridge

**Proto:** `grpc/stock_remote_v1.proto`

**Port:** 8081 (internal to compose network)

**Services exposed by:** Internal APIs (per bank)

**Consumed by:** Management API via `IRemoteStockGrpcGateway`

### RPCs

| RPC | Direction | Purpose |
|-----|-----------|---------|
| `BulkInsertVouchers` | Management → Internal | Export vouchers |
| `GetActiveVoucherCount` | Management → Internal | Stock count |
| `GetTransactions` | Management → Internal | Transaction report |
| `AcquireTransferVouchers` | Management → Internal | Branch transfer in |
| `ReleaseTransferVouchers` | Management → Internal | Branch transfer out |
| `ReplaceEnabledEntities` | Management → Internal | Feature sync |

---

## ExternalStockRemoteBridgeGrpcService

**Exposed by:** External API (port 8081)

**Purpose:** Remote bridge for external stock operations

---

## InternalStockRemoteBridgeGrpcService

**Exposed by:** Internal APIs (port 8081)

**Purpose:** Remote bridge for per-bank stock operations

---

## Related pages

- [API reference](api.md)
- [Configuration reference](configuration-reference.md)
- [gRPC remote stock bridge](../integrations/grpc-remote-stock-bridge.md)
