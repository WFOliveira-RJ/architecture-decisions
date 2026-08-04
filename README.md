# Architecture Decisions — LicenseHub (reference project)

A showcase of **architecture thinking as code**: decision records (ADRs), C4 diagrams, and trade-off analyses for *LicenseHub*, a fictional public-sector licensing platform.

> **Why this repository exists**
> Most of an architect's real work — decisions, trade-offs, context — never shows up in code repositories. This project documents that work explicitly, using a fictional but realistic domain, so the reasoning itself can be read, challenged, and reused.
> All content is original and refers to an invented system. No real project, client, or employer material is included.

## The fictional domain

LicenseHub receives license applications (environmental, operational, sanitary), routes them through configurable review workflows across multiple departments, and issues digitally-signed licenses. Requirements that drive the architecture:

- ~50k applications/year, seasonal peaks (10x in January)
- Auditability: every state change must be traceable for 20 years
- Integrations with 6+ external government systems (payments, identity, document signing)
- Small platform team (4–6 developers), public-sector procurement constraints

## Structure

| Path | Content |
|------|---------|
| [`docs/c4-context.md`](docs/c4-context.md) | C4 Level 1 — system context diagram |
| [`docs/c4-container.md`](docs/c4-container.md) | C4 Level 2 — container diagram |
| [`adr/`](adr/) | Architecture Decision Records, numbered and immutable |
| [`adr/template.md`](adr/template.md) | The ADR template used here |

## Decisions so far

| # | Decision | Status |
|---|----------|--------|
| [0001](adr/0001-record-architecture-decisions.md) | Record architecture decisions as ADRs | Accepted |
| [0002](adr/0002-modular-monolith-over-microservices.md) | Modular monolith over microservices | Accepted |
| [0003](adr/0003-postgresql-as-primary-datastore.md) | PostgreSQL as the primary datastore | Accepted |
| [0004](adr/0004-async-processing-with-message-queue.md) | Asynchronous processing via message queue | Accepted |

## Reading order

Start with the [context diagram](docs/c4-context.md), then [ADR-0002](adr/0002-modular-monolith-over-microservices.md) — it sets the structural direction every later decision builds on.

---

*Maintained by [William F. de Oliveira](https://github.com/WFOliveira-RJ) — Solutions Architect. Feedback and discussion via issues are welcome.*
