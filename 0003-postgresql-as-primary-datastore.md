# ADR-0003: PostgreSQL as the primary datastore

- **Status:** Accepted
- **Date:** 2026-08-03
- **Deciders:** Solutions architect, DBA

## Context

The datastore must hold relational workflow state, an append-only audit log queryable for 20 years, and semi-structured form payloads that vary by license type (30+ form schemas, evolving yearly). Procurement rules favor open-source or already-licensed products; the operations team knows Oracle and PostgreSQL.

## Options considered

1. **Oracle** — already licensed and known; but per-environment licensing complicates the many test/staging environments the workflow engine needs, and ties the audit archive to a paid product for 20 years.
2. **PostgreSQL** — relational + first-class JSONB for the variable form payloads; table partitioning fits the audit archive; zero licensing friction for any number of environments.
3. **PostgreSQL + MongoDB split** (forms in Mongo) — matches the document-shaped data, but adds a second consistency domain and a second operational skillset for data that still needs to join against workflow state constantly.

## Decision

PostgreSQL alone (option 2). JSONB covers the document-shaped data without giving up transactions and joins; partitioned append-only tables cover the audit requirement with plain SQL.

## Consequences

- Form schema evolution is handled with JSONB + versioned validation in the application layer — schema-on-read discipline is documented and tested, since the database no longer enforces it.
- Audit tables are range-partitioned by year, with old partitions moved to cheap storage; restore-and-query of a 15-year-old partition is a rehearsed runbook, not a hope.
- Revisit trigger: if full-text search over documents becomes a first-class feature, evaluate a dedicated search engine *as a read replica*, never as the source of truth.
