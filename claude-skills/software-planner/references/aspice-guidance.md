# ASPICE PAM 4.0 Compliance Guidance

This file maps Automotive SPICE (ASPICE) PAM 4.0 base practices for SYS.1, SYS.2, and SWE.1
to specific artifacts produced by this skill. Use it during Phase 2 to ensure every base
practice is satisfied and to populate the correct work product fields.

---

## Overview

| Process | Name | Purpose |
|---|---|---|
| SYS.1 | Requirements Elicitation | Gather and agree on stakeholder needs |
| SYS.2 | System Requirements Analysis | Specify and analyse system-level requirements |
| SWE.1 | Software Requirements Analysis | Derive software requirements from system requirements |

**Key work products referenced below:**

| WP ID | Work Product Name | Skill artifact |
|---|---|---|
| WP 17-00 | Requirement | `requirements_register` entries |
| WP 17-54 | Requirement Attribute | `status`, `verification_criterion`, `criticality`, `type` fields |
| WP 13-52 | Communications Evidence | Phase 1 Q&A exchange (elicitation record) |
| WP 15-51 | Analysis Results | `risk_register`, open questions, feasibility notes |
| WP 08-52 | Traceability Record | `traceability_matrix` |
| WP 14-52 | Change Request | `change_history` entries in requirements |

---

## SYS.1 — Requirements Elicitation

SYS.1 focuses on identifying, capturing, and agreeing on stakeholder requirements before
system-level requirements are specified.

| BP | Base Practice | Skill artifact |
|---|---|---|
| SYS.1 BP1 | **Obtain stakeholder requirements.** Identify all relevant stakeholders and elicit their needs, expectations, and constraints using structured elicitation techniques. | Phase 1 Question Phase. Every `stakeholder_register` entry (SH-NNN). |
| SYS.1 BP2 | **Understand stakeholder requirements.** Ensure requirements are understood by all stakeholders and that ambiguous or contradictory requirements are resolved before agreement. | Phase 1 restatement + gap analysis. `open_questions` flagging ambiguous requirements. |
| SYS.1 BP3 | **Agree on requirements.** Obtain explicit agreement from relevant stakeholders on the elicited requirements. Mark stakeholders with `review_required: true`. | `stakeholder_register` with `review_required`. Requirement `status: "agreed"` after sign-off. |
| SYS.1 BP4 | **Communicate requirements status.** Track and communicate the status of requirements throughout the project lifecycle. | `status` field on every `requirements_register` entry. Valid values: `"proposed"`, `"agreed"`, `"under_discussion"`, `"rejected"`, `"baseline"`. |

### SYS.1 Phase 1 guidance

The Phase 1 Q&A exchange **is** the WP 13-52 Communications Evidence for SYS.1. When
producing Phase 1 output, always include a section header that makes this explicit:

```
## Requirements Elicitation Record (WP 13-52)
```

All stakeholder questions and their answers should be preserved verbatim. Do not summarise
away disagreements or unresolved items — record them as `open_questions`.

---

## SYS.2 — System Requirements Analysis

SYS.2 takes the elicited stakeholder requirements (SYS.1 output) and transforms them into
a complete, analysed, and traceable set of system requirements.

| BP | Base Practice | Skill artifact |
|---|---|---|
| SYS.2 BP1 | **Specify system requirements.** Document each stakeholder need as a verifiable system requirement with a unique identifier and agreed attributes. | `requirements_register` entries with `id`, `source_text`, `type`, `criticality`, `status`. |
| SYS.2 BP2 | **Structure system requirements.** Organise requirements into a coherent hierarchy or architecture; separate functional from non-functional. | `epics` hierarchy (Epics → Stories). `nfrs` array separate from functional requirements. |
| SYS.2 BP3 | **Analyse system requirements.** Check requirements for correctness, completeness, consistency, feasibility, testability, and absence of design decisions. Flag failures. | Phase 2 Step 4 quality check. `open_questions` with `blocking: true` for failures. |
| SYS.2 BP4 | **Analyse operating environment.** Understand and document constraints imposed by the operating environment (hardware, OS, external systems, regulations). | `constraints` and `assumptions` fields. Integration questions in Phase 1. |
| SYS.2 BP5 | **Ensure consistency and establish traceability.** Verify bidirectional traceability between stakeholder requirements and system requirements; document inconsistencies. | `traceability_matrix` with `requirement_to_stories` and `story_to_requirements`. `coverage_gaps` must be empty. |

### SYS.2 requirements register fields

Every `requirements_register` entry must include:

| Field | ASPICE mapping |
|---|---|
| `id` | Unique identifier (WP 17-00) |
| `source_text` | Verbatim stakeholder need (WP 17-00) |
| `type` | Requirement category — functional / non_functional / constraint / assumption |
| `criticality` | Priority / importance attribute (WP 17-54) |
| `status` | Lifecycle status (WP 17-54 + SYS.1 BP4) |
| `verification_method` | How the requirement will be verified (WP 17-54 + SWE.1 BP3) |
| `verification_criterion` | Observable, measurable condition that demonstrates the requirement is met (WP 17-54 + SWE.1 BP3) |
| `rationale` | Why this requirement exists — supports feasibility analysis |
| `implemented_by` | Forward traceability to Story IDs |
| `change_history` | Change log entries (WP 14-52) |

### SYS.2 baseline

When all `must_have` requirements have `status: "agreed"` and `coverage_gaps` is empty,
record `spec_revision: "1.0.0"` in the spec header. This constitutes the initial
requirements baseline as required by SYS.2.

---

## SWE.1 — Software Requirements Analysis

SWE.1 derives software-level requirements from system requirements (SYS.2 output) and
ensures they are complete, consistent, and verifiable with verification criteria.

| BP | Base Practice | Skill artifact |
|---|---|---|
| SWE.1 BP1 | **Specify software requirements.** Derive software requirements from system requirements, including functional behaviour, interfaces, timing, safety, and regulatory constraints. Separate functional from non-functional. | Stories (functional), NFRs (non-functional). Each Story traces via `source_refs` to parent `REQ-NNN`. |
| SWE.1 BP2 | **Structure software requirements.** Organise software requirements into a hierarchy that reflects the software architecture and supports component-level allocation. | Epics → Stories → Tasks hierarchy. `epic_ref` and `task_ids` cross-references. |
| SWE.1 BP3 | **Define verification criteria.** For each software requirement, specify the verification criteria that will be used to demonstrate the requirement is satisfied. These belong in the requirement itself, not only in test plans. | `verification_criterion` field on every `requirements_register` entry. Every Story has ≥ 1 `test` Task. Acceptance criteria in Given/When/Then form. |
| SWE.1 BP4 | **Analyse operating environment.** Identify and document constraints imposed by the software operating environment (runtime, OS, hardware interfaces, memory, timing). | NFRs for performance, resource, and environmental constraints. Integration Tasks (`infra` type) for external dependencies. |
| SWE.1 BP5 | **Ensure consistency and establish traceability.** Verify bidirectional traceability between system requirements and software requirements; ensure no conflicts. | `traceability_matrix` bidirectional closure check (Phase 2 Step 13). `story_to_requirements` backward links. |

### SWE.1 verification criterion guidance

The `verification_criterion` field must be:

- **Observable**: expressed in terms of a measurable system state or output
- **Unambiguous**: only one interpretation possible
- **Tied to the requirement**: directly proves the specific requirement, not a generic test
- **Method-aligned**: consistent with the `verification_method` field value

Example for a requirement "The system shall process sensor readings within 500 ms":

```json
{
  "verification_method": "test",
  "verification_criterion": "Under a sustained load of 1,000 readings/sec, 95th-percentile
    end-to-end processing latency from sensor ingest to database write is ≤ 500 ms,
    as measured by load-test tooling over a 10-minute run."
}
```

---

## ASPICE compliance checklist

Run this checklist during Phase 2 before delivering the JSON output.

### SYS.1
- [ ] All stakeholders identified with `SH-NNN` IDs
- [ ] Stakeholders requiring approval have `review_required: true`
- [ ] Every requirement has `status` set (start as `"proposed"`, set to `"agreed"` when confirmed)
- [ ] Phase 1 Q&A section labelled as Requirements Elicitation Record (WP 13-52)
- [ ] All unresolved disagreements captured as `open_questions`

### SYS.2
- [ ] Every `requirements_register` entry has `id`, `source_text`, `type`, `criticality`, `status`
- [ ] Functional and non-functional requirements are in separate structures (reqs vs `nfrs`)
- [ ] Quality check (Phase 2 Step 4) run — all ambiguous requirements flagged before proceeding
- [ ] Operating environment constraints recorded (integration constraints, regulatory, hardware)
- [ ] `traceability_matrix` complete with no `coverage_gaps`
- [ ] `spec_revision` set — this is the requirements baseline

### SWE.1
- [ ] Every `requirements_register` entry has `verification_criterion`
- [ ] Every Story has ≥ 1 `test` Task
- [ ] Every acceptance criterion is in Given/When/Then form
- [ ] NFRs cover all software operating environment constraints (performance, resource, timing)
- [ ] Bidirectional traceability verified: `requirement_to_stories` + `story_to_requirements` match

---

## Requirement `status` lifecycle

```
proposed → under_discussion → agreed → baseline
                         ↓
                      rejected
```

| Status | When to use |
|---|---|
| `proposed` | Requirement has been elicited but not yet reviewed by stakeholders |
| `under_discussion` | Stakeholder review raised questions or objections |
| `agreed` | All relevant stakeholders have confirmed the requirement |
| `rejected` | Stakeholder review determined this requirement is out of scope or infeasible |
| `baseline` | Agreed requirements locked in an approved release baseline |

**Default for Phase 2 output:** Set `status: "proposed"` unless the user's answers in
Phase 1 explicitly confirm stakeholder agreement, in which case use `"agreed"`. Note in
`assumptions` that a formal baseline requires stakeholder sign-off outside this tool.
