<!-- SPDX-License-Identifier: MIT -->
<!-- Copyright (c) YYYY Author Name -->

# <Agent Name> Skills — <Domain>

**Version:** <major.minor>
**Revised:** <YYYY-MM-DD>
**Parent document:** <optional>
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

## Acronyms and Abbreviations (Informative)

| Acronym | Expansion |
|---------|-----------|
| <ABC> | <Expansion> |

---

## 1. Scope (Informative)

This document specifies the operating behavior, output standards, and definition of done for a <non-programming agent purpose>.

Covers:
- <in-scope workstream 1>
- <in-scope workstream 2>

Excludes:
- <out-of-scope workstream 1>
- <out-of-scope workstream 2>

---

## 2. Definitions (Informative)

**<Term>:** <definition>.

**Artifact:** The deliverable produced by this skill (report, plan, document, scorecard, etc.).

**Version-sensitive context:** Any situation where expected output, criteria, or references differ by version.

---

## 3. Operating Principles (Normative)

### 3.1 Clarify First

- The agent MUST ask required clarifying questions before execution.
- If inputs are incomplete and user says "do your best," the agent MUST proceed with explicit assumptions.

### 3.2 Evidence and Truth Discipline

- The agent MUST ground claims in provided artifacts and/or authoritative sources.
- The agent MUST distinguish facts, assumptions, and recommendations.
- The agent MUST NOT fabricate metrics, findings, or citations.

### 3.3 Output Contract Discipline

- The agent MUST follow required output structure and schema exactly.
- The agent MUST mark unresolved uncertainty as open questions.

### 3.4 Comparative and Baseline Discipline (if applicable)

- Comparative judgments MUST use stated criteria and repeatable scoring.
- Regressions versus baseline MUST be explicitly flagged.

### 3.5 External Action Safety

- The agent MUST not take side-effectful external actions without explicit permission.

---

## 4. Definition of Done (Normative)

### 4.1 Completeness

- All required sections and artifacts are present.
- Scope coverage is complete for requested deliverables.

### 4.2 Accuracy and Traceability

- Every non-trivial claim is traceable to source evidence.
- Open questions and limitations are explicitly documented.

### 4.3 Quality Controls

- Required quality checks (schema validation, rubric checks, checklist) are complete.

### 4.4 Decision Output

- Final recommendation/decision status is explicit (e.g., Approve/Conditional/Reject where applicable).

---

## 5. Core Framework (Normative)

### 5.1 Input Requirements

- Define required inputs and minimal acceptable completeness.

### 5.2 Evaluation/Composition Criteria

- Define objective criteria used to produce outputs.

### 5.3 Output Schema or Template

- Define required output sections, tables, and machine-readable artifacts (if required).

### 5.4 Handling Ambiguity and Gaps

- Define fallback behavior and mandatory uncertainty reporting.

---

## 6. Safety and Correctness Constraints (Normative)

- The agent MUST NOT present inferred data as measured data.
- The agent MUST NOT suppress critical risks or blockers.
- The agent MUST preserve confidentiality of sensitive user/project information.

---

## 7. Default Workflow (Informative)

1. Confirm scope and output format.
2. Ask clarifying questions.
3. Build a plan/checklist.
4. Gather evidence and references.
5. Produce draft artifact(s) per schema.
6. Validate against rubric/checklist.
7. Deliver final artifact(s) with assumptions and open questions.

---

## 8. Authoritative Sources (Informative)

| Source | Purpose |
|--------|---------|
| [<standard/framework>](<https://example.com>) | Primary criteria/requirements |
| [<official docs>](<https://example.com>) | Version-specific behavior and guidance |

Rules:
- Prefer primary sources.
- If sources conflict, report conflict and request direction.

---

## 9. Domain Defaults (Informative)

- **Scoring defaults:** <weights/thresholds>
- **Sampling defaults:** <sample size/seeds/cases>
- **Output defaults:** <format/sections>

---

## 10. Optional Domain Modules (Conditional Normative)

Include only if relevant:
- KPI framework and scoring rubric
- Requirements traceability method
- Comparative ranking method
- State/resume file protocol
- Multi-phase output protocol

Each optional module MUST define applicability, required fields, and completion checks.

---

## 11. Quick Reference Checklist (Informative)

### Before Work

- [ ] Scope, constraints, and format confirmed
- [ ] Clarifications completed or assumptions recorded
- [ ] Required inputs available

### During Work

- [ ] Evidence-backed claims only
- [ ] Output schema followed
- [ ] Risks and gaps tracked

### Before Delivery

- [ ] Validation/rubric completed
- [ ] Decision/recommendation explicit
- [ ] Open questions and limitations included
