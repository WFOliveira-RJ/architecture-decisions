# ADR-0001: Record architecture decisions as ADRs

- **Status:** Accepted
- **Date:** 2026-08-03
- **Deciders:** Solutions architect

## Context

Architecture knowledge on most projects lives in slides, meeting notes and people's heads. Team turnover — common in public-sector projects with outsourced staff — erases the *why* behind the system, and past decisions get relitigated or accidentally reversed.

## Options considered

1. **Wiki pages** — easy to write, but no lifecycle: pages rot, and nothing marks a decision as superseded.
2. **Design documents per feature** — good for big efforts, too heavy for the many small decisions that actually shape a system.
3. **ADRs in the repository** — short, numbered, immutable records next to the code they govern; superseding is explicit.

## Decision

Every decision that is expensive to reverse gets an ADR in this repository, using [the template](template.md). ADRs are immutable once accepted; changing course means a new ADR that supersedes the old one.

## Consequences

- New team members can reconstruct the reasoning chain by reading `adr/` in order.
- Writing the Context section forces constraints to be stated explicitly — several "obvious" choices died at this stage.
- Cost: discipline. An ADR that takes more than an hour to write is covering too much; split it.
