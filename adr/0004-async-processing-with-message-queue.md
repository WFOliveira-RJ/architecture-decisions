# ADR-0004: Asynchronous processing via message queue

- **Status:** Accepted
- **Date:** 2026-08-03
- **Deciders:** Solutions architect, tech lead

## Context

LicenseHub depends on six external government systems with wildly different reliability (the payment webhook can arrive hours late; the signature service has scheduled downtime windows). Handling these synchronously inside API requests would couple user-facing latency to the slowest external dependency and lose work on failures.

## Options considered

1. **Synchronous calls with retries** — simplest, but retry storms during external outages would exhaust API threads exactly when the system is under pressure, and long-running work (report generation) doesn't fit a request/response cycle at all.
2. **Database-as-queue (polling a jobs table)** — no new infrastructure and transactionally safe; workable at our scale, but delivery semantics, backoff, dead-lettering and visibility all become bespoke code we maintain forever.
3. **Message broker (RabbitMQ)** — mature delivery semantics (acks, retries, DLQs), natural worker scaling, and an explicit contract between the API and the workers; costs one more piece of infrastructure.

## Decision

RabbitMQ (option 3), with a narrow usage contract: the API publishes *commands* (do this work) and *domain events* (this happened) after the database transaction commits (transactional outbox pattern); workers are idempotent consumers.

## Consequences

- API latency depends only on LicenseHub itself; external flakiness lands in queues and dead-letter queues where it is visible and replayable.
- The outbox pattern adds a table and a relay, which is the price of never publishing an event for a transaction that rolled back.
- Broker choice is deliberately boring: our messaging needs (work queues, modest volume) don't justify a streaming platform; Kafka becomes worth revisiting only if event *replay for new consumers* becomes a real requirement, which the outbox table partially covers anyway.
