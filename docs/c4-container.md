# C4 — Level 2: Containers

Inside the LicenseHub system boundary. Structure follows [ADR-0002](../adr/0002-modular-monolith-over-microservices.md) (modular monolith) and [ADR-0004](../adr/0004-async-processing-with-message-queue.md) (async workers).

```mermaid
C4Container
    title Container Diagram — LicenseHub

    Person(applicant, "Applicant")
    Person(staff, "Reviewer / Manager")

    System_Boundary(lh, "LicenseHub") {
        Container(web, "Web Application", "React SPA", "Application forms, tracking, review workspace")
        Container(api, "Core API", "Java 21 / Spring Boot", "Modular monolith: applications, workflow, licensing, integrations modules")
        Container(worker, "Async Workers", "Java 21 / Spring Boot", "Payment reconciliation, signing, notifications, report generation")
        ContainerDb(db, "Primary Database", "PostgreSQL", "Applications, workflow state, audit log (append-only)")
        ContainerQueue(mq, "Message Broker", "RabbitMQ", "Work queues + domain events")
        Container(files, "Object Storage", "S3-compatible", "Submitted documents and issued licenses")
    }

    System_Ext(ext, "External gov systems", "Identity, payments, signature, geo, notifications")

    Rel(applicant, web, "Uses", "HTTPS")
    Rel(staff, web, "Uses", "HTTPS")
    Rel(web, api, "JSON/HTTPS")
    Rel(api, db, "JDBC")
    Rel(api, files, "S3 API")
    Rel(api, mq, "Publishes commands/events")
    Rel(worker, mq, "Consumes")
    Rel(worker, db, "JDBC")
    Rel(worker, ext, "REST/webhooks")
    Rel(api, ext, "REST (synchronous validations)")
```

## Key properties

- **One deployable API** (modular monolith) + **one worker deployable** — small team, low operational surface, clean async boundary.
- The **audit log is append-only** in PostgreSQL ([ADR-0003](../adr/0003-postgresql-as-primary-datastore.md)) — the 20-year traceability requirement is a data-design concern, not a separate system.
- Everything that talks to slow/unreliable external systems runs in workers, keeping API latency predictable.
