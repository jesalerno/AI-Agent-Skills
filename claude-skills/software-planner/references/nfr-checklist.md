# NFR Checklist

For every NFR category below, check whether the source document addresses it. If not addressed,
generate a question in Phase 1 or record an `open_question`. Every captured NFR must have a
measurable acceptance criterion — not "the system must be fast" but "p99 response time < 500 ms
under 1,000 concurrent users."

| Category | Key questions to assess |
|---|---|
| **Performance** | Response time targets, throughput (req/s), batch job SLAs |
| **Scalability** | Horizontal vs. vertical scaling strategy, data volume growth, user growth |
| **Availability** | Uptime SLA (e.g., 99.9%), planned maintenance windows, geographic redundancy |
| **Reliability** | Error rate tolerance, retry/backoff strategy, idempotency requirements |
| **Safety (Functional)** | Does system failure cause physical injury, death, or environmental damage? Safety integrity level required (ASIL per ISO 26262, SIL per IEC 61508, DAL per DO-178C)? Fail-safe vs. fail-operational behaviour? IV&V required? Named safety standards mandated? |
| **Timing / Real-time** | Hard vs. soft real-time deadlines? WCET bounds? End-to-end latency budget? Periodic task cycle times? Scheduling constraints? |
| **Security** | Authentication, authorization, encryption, audit, compliance (see STRIDE guidance) |
| **Compliance** | Regulatory framework (GDPR, HIPAA, PCI-DSS, SOC 2, ISO 27001), data residency, named coding standards |
| **Disaster Recovery** | RTO (max downtime), RPO (max data loss), backup frequency and retention |
| **Maintainability** | Code coverage minimums, documentation standards, dependency update cadence |
| **Observability** | Logging format and level, metrics, distributed tracing, alerting thresholds |
| **Accessibility** | WCAG 2.1 conformance level (A/AA/AAA), EN 301 549, or Section 508. Which surfaces (web, mobile, desktop)? Which assistive technologies must be tested? Who performs accessibility testing and when? |
| **Localization** | Which languages/locales at launch and in future phases? RTL support needed? Locale-specific formatting (date, time, currency, numbers)? Translation workflow (dev-owned strings vs CMS vs external translation service)? |
| **Portability** | Deployment target constraints, containerization, cloud-vendor independence |
| **Data Retention** | How long data must be kept, when and how it must be deleted (right to erasure) |

## Default NFRs for user-facing systems

Unless the system is exclusively internal tooling with no end users, include both of the
following NFRs and document explicitly if they genuinely don't apply.

| NFR | Minimum acceptance criterion |
|---|---|
| Accessibility | All user-facing screens conform to WCAG 2.1 Level AA. Verified by automated scan (axe-core or equivalent) plus manual testing with at least one screen reader (e.g., NVDA, VoiceOver, TalkBack). Zero critical or serious axe violations in CI pipeline. |
| Localization | All user-facing strings are externalized to resource files. The application renders correctly in all supported locales, including locale-appropriate date/time/currency/number formatting. RTL layout is correct where applicable. No hard-coded locale-specific strings anywhere in the codebase. |

---

## Mandatory security NFRs (always include unless explicitly inapplicable)

| NFR | Minimum acceptance criterion |
|---|---|
| Authentication | All non-public endpoints require a verified identity before access is granted |
| Authorization | Every resource access is checked against the actor's permissions; no resource is accessible by default |
| Data in transit | All communication uses TLS 1.2 or higher |
| Data at rest | All sensitive or personal data is encrypted at rest using AES-256 or equivalent |
| Secret management | No secrets, credentials, or API keys appear in source code, logs, or error responses |
| Audit logging | All auth events, authorization failures, and data mutations are logged with actor, timestamp, and resource identifier |
| Dependency hygiene | All third-party dependencies are scanned for known vulnerabilities before each release |
| Error handling | No stack traces, internal paths, or system details are exposed in responses to clients |

## Security story triggers

Create a dedicated security Story (or add security Tasks to an existing Story) whenever any of
these conditions are present:

- User registration, login, logout, or password management
- Role or permission assignment
- Storage or transmission of PII, financial data, health data, or credentials
- File upload or download
- External API integration or webhook consumption
- Admin or privileged function
- Public-facing endpoint
- Scheduled job or background task with elevated privileges
