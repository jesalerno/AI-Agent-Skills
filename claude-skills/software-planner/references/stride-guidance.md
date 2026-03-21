# STRIDE Threat Modeling Guidance

Run a STRIDE analysis for every specification. A threat category is inapplicable only when the
system has no components susceptible to that threat class (e.g., a system with no user identity
has no Spoofing surface). When a category does not apply, state that determination explicitly in
the `threat_model` — never omit a category silently.

For each applicable category:
1. Describe the specific threat in **this system's context** — not a generic definition.
2. Assign `likelihood` (high/medium/low), `impact` (high/medium/low), and `risk_level`
   (critical = high×high; high = high×medium or medium×high; medium = medium×medium or
   low×high; low = low×low or low×medium or medium×low).
3. Create at least one `security`-typed Task that mitigates the threat.
4. Reference that Task's ID in `mitigated_by`.

## STRIDE categories and typical mitigations

| Category | What it covers | Common mitigations |
|---|---|---|
| **Spoofing** | Impersonating another user, system, or service | MFA, mutual TLS, signed JWT tokens, certificate pinning, strong password hashing (bcrypt/Argon2) |
| **Tampering** | Modifying data in transit or at rest | Input validation, parameterized queries (prevent SQL injection), checksums/HMACs, signed payloads, HTTPS everywhere |
| **Repudiation** | Denying that an action was performed | Append-only audit logs, non-repudiation tokens, cryptographic timestamp signing, immutable log storage |
| **Information Disclosure** | Exposing data to unauthorized parties | Least-privilege access, field-level encryption, output sanitization, CORS policy, PII masking in logs |
| **Denial of Service** | Making the system unavailable | Rate limiting, input size limits, circuit breakers, resource quotas, CDN/WAF, connection pooling |
| **Elevation of Privilege** | Gaining permissions beyond what was granted | Principle of least privilege, role enforcement at every layer, privilege separation, no client-side authorization |

## Risk level derivation matrix

|  | High impact | Medium impact | Low impact |
|---|---|---|---|
| **High likelihood** | critical | high | medium |
| **Medium likelihood** | high | medium | low |
| **Low likelihood** | medium | low | low |
