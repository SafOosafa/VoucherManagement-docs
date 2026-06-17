# MITF Voucher Provider — documentation

**[Masarat](https://masarat.ly/)** builds financial infrastructure for banks and regulated partners. **MITF Voucher Provider** is the platform for **digital voucher stock management**: central stock custody, per-bank inventory, channel purchase flows, third-party integrations, and admin operations — anchored in a **microservice ledger** so finance, partners, and auditors share one source of truth.

---

## Where to go next

| You are... | Start here |
|---|---|
| **QA / UAT** | [UI testing overview](flows/ui-testing-overview.md) |
| **API integration** | [Service reference index](reference/) |
| **Reports / third-party stock** | [Reports API guide](reference/reports-api-guide.md) |
| **System flows** | [Flow diagrams & architecture](architecture/flow-diagrams.md) |
| **Docker / CI / local stack** | [Production deployment](operations/production-deployment.md) |
| **Platform specification** | [Platform capabilities](architecture/README.md) |
| **Every page listed** | [Full A–Z index](getting-started/all-pages.md) |
| **Release and doc updates** | [Changelog & releases](changelog/) |

---

## Quick reference — local URLs

| Service | URL | Port |
|---------|-----|------|
| Management API | http://localhost:5189/swagger | 5189 |
| External API | http://localhost:5252/swagger | 5252 |
| Gateway | http://localhost:5089/swagger | 5089 |
| Internal (Jumhoria) | http://localhost:5238/swagger | 5238–5243 |
| Purchase orchestrator | http://localhost:5260/swagger | 5260 |
| Stock SPA | http://localhost:4200 | 4200 |

Dev login (when seed enabled): `admin` / `admin`

---

[masarat.ly](https://masarat.ly/) · [This site on GitHub](https://github.com/anstwechy/mitf_wallet_public_docs)
