# Load testing operations

## Test pyramid

| Layer | Tool | Scope |
|-------|------|--------|
| API | Newman / Postman | Contracts, auth, business rules |
| UI (CRUD) | Playwright | Full feature flows with tags |
| UI (smoke) | Maestro | Read-only route smoke |

---

## Load testing tools

| Tool | Purpose |
|------|---------|
| Newman | API contract and business rule regression |
| Playwright | UI CRUD journey tests (browser automation) |
| Maestro | Read-only UI smoke tests (fast route coverage) |

---

## CI integration

| Trigger | Newman | Playwright |
|---------|--------|------------|
| PR → `dev` | Full collection | Smoke: `auth`, `dashboard`, `import-flow` |
| Push → `dev` | Full collection | Full suite |
| Nightly (02:00 UTC) | Full collection | Full suite |
| Manual dispatch | Full collection | Full suite; optional `primo_e2e` |

---

## Related pages

- [Stakeholder load test summary](../load-testing/README.md)
- [Load test reference runs](../load-testing/load-test-reference-runs.md)
- [UI testing overview](../flows/ui-testing-overview.md)
