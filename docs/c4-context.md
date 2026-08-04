# C4 — Level 1: System Context

LicenseHub sits between citizens/companies applying for licenses and the government systems that support the process.

```mermaid
C4Context
    title System Context — LicenseHub

    Person(applicant, "Applicant", "Citizen or company applying for a license")
    Person(reviewer, "Reviewer", "Government analyst reviewing applications")
    Person(manager, "Department Manager", "Approves and signs licenses")

    System(licensehub, "LicenseHub", "Receives, routes and decides license applications; issues signed licenses")

    System_Ext(idp, "Gov Identity Provider", "Authentication / SSO for citizens and staff")
    System_Ext(pay, "Payment Gateway", "Collects application fees")
    System_Ext(sign, "Digital Signature Service", "Signs issued licenses")
    System_Ext(geo, "Geospatial Registry", "Validates locations and zoning")
    System_Ext(notify, "Notification Hub", "E-mail/SMS delivery")

    Rel(applicant, licensehub, "Submits and tracks applications", "HTTPS")
    Rel(reviewer, licensehub, "Reviews and annotates applications", "HTTPS")
    Rel(manager, licensehub, "Approves and issues licenses", "HTTPS")
    Rel(licensehub, idp, "Delegates authentication", "OIDC")
    Rel(licensehub, pay, "Creates charges, receives confirmations", "REST + webhook")
    Rel(licensehub, sign, "Requests document signature", "REST")
    Rel(licensehub, geo, "Validates addresses/zoning", "REST")
    Rel(licensehub, notify, "Sends status notifications", "Queue")
```

## Notes

- All human access goes through the Gov Identity Provider — LicenseHub stores no passwords ([ADR-0002](../adr/0002-modular-monolith-over-microservices.md) discusses the internal structure behind the single system boundary shown here).
- Payment confirmation is asynchronous by nature (webhook), which is one of the drivers for [ADR-0004](../adr/0004-async-processing-with-message-queue.md).
