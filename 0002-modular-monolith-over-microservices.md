# ADR-0002: Modular monolith over microservices

- **Status:** Accepted
- **Date:** 2026-08-03
- **Deciders:** Solutions architect, tech lead

## Context

LicenseHub has clear domain seams (applications, workflow, licensing, integrations) that *suggest* microservices. But the platform team is 4–6 developers, the operational environment is a government datacenter with limited platform tooling, and the traffic profile (~50k applications/year, 10x January peak) is modest by web standards. The 20-year auditability requirement makes data consistency errors extremely expensive.

## Options considered

1. **Microservices per domain** — independent deploys and scaling; but 4 services × (pipeline + observability + on-call surface) for a 5-person team is an operations tax paid daily, and cross-service consistency for the audit trail would require sagas or distributed transactions — the hardest possible version of our most critical requirement.
2. **Classic layered monolith** — simplest to run, but experience shows package-by-layer structures decay into a tangle where every feature touches everything.
3. **Modular monolith** — one deployable, internally split into modules with enforced boundaries (separate Gradle modules + ArchUnit tests; modules communicate through interfaces and domain events, never each other's tables).

## Decision

Modular monolith (option 3). The team size and the consistency-critical audit requirement dominate: one database transaction can cover a state change *and* its audit record, and one deployable keeps the operational surface proportional to the team.

## Consequences

- Module boundaries are enforced by build and test tooling, so a future extraction to a service is a *move*, not a rewrite. The extraction trigger is explicit: sustained divergent scaling needs or a second team taking ownership of a module.
- Single deployable means a bad release affects everything — mitigated by the async workers being a separate deployable ([ADR-0004](0004-async-processing-with-message-queue.md)) and by feature flags for risky changes.
- We accept that January peaks are handled by scaling the whole API horizontally, which is slightly wasteful and entirely fine at this scale.
