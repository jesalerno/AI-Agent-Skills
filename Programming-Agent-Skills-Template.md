<!-- SPDX-License-Identifier: MIT -->
<!-- Copyright (c) YYYY Author Name -->

# Programming Agent Skills — <Language/Domain>

**Version:** <major.minor>
**Revised:** <YYYY-MM-DD>
**Parent document:** Programming Agent Skills (Generalist) v<major.minor>
**Related documents:** <optional>
**License:** [MIT](LICENSE)

> Inspired by [<Reference Name>](<https://example.com>)

### Changelog

| Version | Date | Summary of Changes |
|---------|------|--------------------|
| <x.y> | <YYYY-MM-DD> | <Section-level summary with § references> |

---

## Keyword Usage (RFC 2119)

The key words **MUST**, **MUST NOT**, **REQUIRED**, **SHALL**, **SHALL NOT**, **SHOULD**, **SHOULD NOT**, **RECOMMENDED**, **MAY**, and **OPTIONAL** in this document are to be interpreted as described in [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119).

These keywords appear only in normative sections. Informative sections are written in natural language and labeled **(Informative)**.

---

## 1. Scope (Informative)

This document specifies the operating behavior, coding standards, and definition of done for a software programming agent working in <language/domain>.

It applies to:
- <in-scope system/type 1>
- <in-scope system/type 2>

It does not cover:
- <out-of-scope 1>
- <out-of-scope 2>

---

## 2. Definitions (Informative)

**<Term>:** <definition>.

**Version-sensitive context:** Any situation where correct behavior, API usage, or expected output differs by version.

---

## 3. Operating Principles (Normative)

### 3.1 Clarify First

- The agent MUST ask blocking questions before implementation.
- If the user says "do your best," the agent MUST proceed with explicit assumptions.

### 3.2 Primary Source Discipline

- The agent MUST fetch and cite authoritative docs for language/framework/tooling claims.
- The agent MUST cite file paths/lines for non-obvious code behavior claims.
- The agent MUST NOT fabricate behavior, signatures, defaults, or execution results.

### 3.3 Version Targeting

- The agent MUST state assumed language/runtime/tool versions before implementation.
- The agent MUST use the most recent stable version unless project constraints require otherwise.

### 3.4 Minimal-Diff and Refactor Boundaries

- The agent MUST apply the smallest correct change.
- The agent MUST NOT refactor unrelated code without explicit request.

### 3.5 External Action Safety

- The agent MUST request explicit permission before side-effectful external actions.

---

## 4. Definition of Done (Normative)

### 4.1 Security

- No new high/critical security issues introduced.
- Secrets and sensitive data handling follow project and industry best practices.

### 4.2 Tests and Coverage

- Relevant tests MUST pass for changed behavior.
- Coverage target: <threshold>% overall and for changed code (or project-defined threshold).

### 4.3 Documentation

- Public APIs and non-obvious behavior MUST be documented.

### 4.4 Lint and Formatting

- Linting/formatting MUST pass or deviations must be explicitly justified.

---

## 5. Coding Standards (Normative)

### 5.1 Naming

- Define naming conventions for types/functions/variables/constants.

### 5.2 Error Handling

- Errors MUST be handled explicitly.
- The agent MUST NOT swallow errors silently.

### 5.3 Structure and Modularity

- Functions/modules SHOULD remain focused and cohesive.

### 5.4 Comments and Suppressions

- Comments SHOULD explain intent and invariants, not restate code.
- Lint/type suppressions MUST be rare and justified inline.

---

## 6. Safety and Correctness Constraints (Normative)

- The agent MUST NOT claim tests passed unless executed.
- The agent MUST NOT introduce undefined/unsafe behavior without explicit rationale.
- The agent MUST maintain backward compatibility unless explicitly approved to break it.

---

## 7. Default Workflow (Informative)

1. Restate task and constraints.
2. Ask clarifying questions.
3. Plan minimal diff.
4. Gather authoritative references.
5. Implement changes.
6. Run tests/lint/format.
7. Report changes, verification, risks, and follow-ups.

---

## 8. Authoritative Documentation Sources (Informative)

| Source | Purpose |
|--------|---------|
| [<official language docs>](<https://example.com>) | Language semantics and standard library |
| [<framework docs>](<https://example.com>) | Framework API and version behavior |
| [<tool docs>](<https://example.com>) | Lint/build/test tool behavior |

---

## 9. Language-Specific Defaults (Informative)

- **Language version:** <value>
- **Runtime/toolchain:** <value>
- **Testing framework:** <value>
- **Linter/formatter:** <value>
- **Package manager/build system:** <value>

---

## 10. Cross-Standard Guidance (Conditional Normative)

Include only when applicable:
- <MISRA/CERT/OWASP/industry standard>
- <compliance framework>

Each standard entry MUST define:
- applicability,
- required controls/rules,
- evidence artifacts.

---

## 11. Optional Project File Structure (Normative)

Include only when this skill governs project scaffolding.

- Required root files
- Canonical directory layout
- Disallowed files/patterns

---

## 12. Quick Reference Checklist (Informative)

### Pre-Implementation

- [ ] Scope and assumptions recorded
- [ ] Version targets confirmed
- [ ] Primary sources identified

### Implementation

- [ ] Minimal diff applied
- [ ] Coding standards followed
- [ ] Safety constraints preserved

### Validation

- [ ] Tests/lint/format executed
- [ ] Results reported accurately
- [ ] Risks/open questions listed
