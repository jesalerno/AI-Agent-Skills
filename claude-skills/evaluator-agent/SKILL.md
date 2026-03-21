---
name: evaluator-agent
description: >
  Use this skill whenever a user wants to evaluate, audit, benchmark, or assess the quality
  and performance of an AI agent or its instruction set. Trigger on phrases like "evaluate
  this agent", "audit these instructions", "benchmark agent performance", "pre-deployment
  review", "check agent quality", "run evals on", "score this instruction set", or any
  request to assess code generation or task automation agents. Also trigger when the user
  wants to compare multiple implementations of a spec, measure compliance with a requirements
  document, assess the visual or aesthetic quality of agent-generated outputs (images, art,
  media), or rank several AI solutions side-by-side. Trigger when the user mentions KPIs like
  FCR, TCR, SVR, hallucination rate, or instruction adherence, or asks for a structured
  evaluation report. Use even if the request is partial (e.g., "just check the instruction
  quality", "compare these five outputs", or "does this meet the spec?").
---

# Evaluator Agent Skill

This skill governs how to conduct structured evaluations of AI agents and their instruction
sets. It applies to **code generation agents**, **task automation agents**,
**output-generative agents** (agents that produce visual, audio, or other media artifacts),
or any combination. All evaluations produce a `.docx` human-readable report, a `.json`
machine-readable summary, and a `methodology-prompt.md` reusable evaluation prompt.

> Based on: Evaluator Agent Skills v1.2 (2026-03-21)
> Inspired by OpenAI Evals, AgentBench, and SWE-bench.
> v1.1 adds: output-generative agent type, Output Artifact Quality KPIs, multi-implementation
> comparative evaluation, requirements traceability methodology, and seed-dependency guidance.
> v1.2 adds: mandatory full evidence matrices alongside summaries, universal failure escalation
> to executive summary, methodology prompt as a third required output artifact, deferred
> executive summary generation, spec coverage gap detection, and multi-report consolidation
> guidance.

---

## Step 1: Ingest and Catalogue

Log the following before doing anything else:
- `agent_id`, `version`, `evaluation_date`, `evaluator_model`, `triggering_engineer`
- Confirm all artifacts are present: instruction set document, model identifier, version tag, and test suite (or generation plan).

---

## Step 2: Clarify Scope (REQUIRED — do not skip)

Present ALL of the following questions in a **single interaction**. Do not proceed until
all answers are received and documented.

**Scope**
1. What type(s) of agent is under evaluation?
   - (a) Code generation agent — produces source code
   - (b) Task automation agent — executes multi-step workflows
   - (c) Output-generative agent — produces visual, audio, or other media artifacts
   - (d) Multiple implementations of the same spec being compared (select all that apply above)
2. What prompted this evaluation? (routine audit / performance regression / pre-deployment review / post-incident review / instruction update)
3. What is the evaluation depth? (a) full evaluation across all KPIs, (b) targeted evaluation of specific dimensions, or (c) instruction-only review with no live execution.
4. Which version of the agent and instruction set is under evaluation? Is a prior version available for baseline comparison? If multiple implementations are being compared, list all of them.

**Test Suite**
5. Has a test suite been provided? If not, should one be generated, and based on what specification or domain?
6. What domain or task category should test cases cover?
7. Are there known failure modes or edge cases the evaluation must specifically cover?

**Output and Reporting**
8. Who is the primary reader of the evaluation report? (individual engineer / team lead / executive review)
9. Should the report include specific rewrite suggestions for instructions, or flag issues only?
10. Is there a deadline that requires prioritizing any evaluation dimension?

State the evaluation defaults (§ Defaults below) explicitly and ask for corrections.

---

## Step 3: Share Preliminary Best-Practices Assessment

Before live execution, present a first-pass review of the instruction set annotated with
**Pass / Partial / Fail** ratings against the operating principles in the Normative Rules
section below. This gives the triggering engineer a quality signal early.

**Important constraints on Step 3 ratings:**
- Ratings MUST be based solely on reading the instruction set document. Do NOT infer
  quality findings from scope answers (e.g., known failure modes mentioned in Q7).
- Known failure modes reported in Q7 are test-focus signals for Step 5, not instruction
  quality evidence. Note them separately as a "Test Focus Areas" callout — do not pre-rate
  instruction quality dimensions based on them.
- Any dimension not yet reviewed MUST be rated **Pending**, not Partial or Fail.

---

## Step 4: Assess Instruction Quality

Score all Instruction Quality KPIs (see KPI Framework). Produce a findings log with:
- Severity: Critical / Major / Minor
- Affected section and line reference
- Rewrite recommendation (required for Critical and Major)

If the evaluation involves a **requirements specification** (not an agent instruction set),
apply requirements traceability methodology instead of or alongside the standard KPI framework.
See the Requirements Traceability Methodology section below.

---

## Step 5: Construct or Validate the Test Suite

Defaults (unless scope says otherwise):
- ≥ 30 total test cases per task domain
- ≥ 20 typical cases
- ≥ 5 edge cases
- ≥ 3 adversarial inputs
- ≥ 2 regression cases from known failure modes

If fewer than 30 are used, document the reason in the report.
Define ground truth **before** running any test case.

**For output-generative agents**: outputs such as images or media are inherently
seed-dependent. Where possible, average quality metrics across ≥ 20 random seeds per
implementation to reduce variance. When only a single seed is used (e.g., for a quick
comparison), document this as a **Scope Limitation** in the report header and note that
results may not generalise across seeds. Use a fixed seed (default: 42) for any
reproducibility-critical runs.

---

## Step 6: Execute Live Performance Evaluation

Run the test suite in an **isolated sandbox**. The evaluated agent MUST NOT interact with
production systems or share context/memory with the evaluator.

For each KPI:
1. Record the raw value
2. Compare to target threshold
3. Classify as Pass / Warning / Fail

Use temperature=0, seed=42 for all generative calls. Log all model parameters.

For code generation agents: run at least one SAST tool (Bandit for Python; Semgrep for
multi-language). Report tool name, version, and findings.

### Step 6a: Output Artifact Quality Assessment (output-generative agents only)

When the agent produces visual or media artifacts, compute Output Artifact Quality KPIs
from `references/kpis.md` § 5.4 in addition to the standard performance KPIs. The goal
is to capture both **technical quality** (fidelity, coverage, complexity) and **aesthetic
quality** (perceptual smoothness, colour harmony, visual balance) as separate sub-scores,
then combine them into a weighted composite.

Recommended composite weights for visual outputs (adjust per domain and document in report):
- Technical Quality: 55%
- Aesthetic Quality: 45%

Explain any deviation from these defaults in the report. Use Python with NumPy, Pillow,
scikit-image, and scikit-learn (KMeans for palette extraction) for metric computation.

---

## Step 7: Baseline Comparison and Regression Analysis

If a prior version is available:
- Produce a delta scorecard for all KPIs
- Flag any KPI that regressed by more than 5 percentage points
- Identify the most likely correlated instruction change

**Multi-implementation comparative evaluation**: When multiple implementations of the same
spec are being evaluated simultaneously (rather than one agent vs. a prior version), produce
a **Comparative Ranking Table** instead of (or in addition to) a delta scorecard. The table
should show all implementations ranked by overall score, with separate sub-rankings for each
KPI category. Highlight where implementations have complementary strengths — for example,
one may score high on technical correctness while another leads on aesthetic quality. See
`references/report-template.md` § 7.8 for the required table structure.

---

## Step 8: Adversarial and Red-Team Testing

Required before any **Approve** deployment decision. Cover:
- Prompt injection
- Conflicting-constraint inputs
- Resource-exhaustion scenarios
- Deliberate tool parameter mis-specification

---

## Step 9: Generate Report

Deliver three output artifacts:
1. **`.docx`** — human-readable report (primary deliverable)
2. **`.json`** — machine-readable summary (see JSON schema in `references/report-template.md`)
3. **`methodology-prompt.md`** — reusable evaluation prompt capturing the methodology used
   (see Methodology Prompt section below)

See `references/report-template.md` for the full required `.docx` structure.

### Report generation order

Generate report sections in this sequence, and **do not write the Executive Summary until
all evidence sections are complete**:

1. Report Header
2. KPI Scorecard (all dimensions)
3. Instruction Quality Findings
4. Performance Findings
5. Baseline Comparison (if applicable)
6. Requirements Traceability Matrix (if applicable) — full evidence matrix, not just the summary
7. Output Artifact Quality (if applicable) — raw metrics table + composite table
8. Multi-Implementation Comparative Ranking (if applicable)
9. Appendix
10. **Executive Summary — write last**, after reviewing all findings above. Before writing,
    scan all scored dimensions for universal verdicts (PASS, PARTIAL, or FAIL across every
    implementation) and surface these explicitly in the summary. A universal FAIL on any
    requirement or KPI category indicates a spec gap, not an implementation weakness, and
    must be called out as a distinct finding.

Deployment Decision must be one of: **Approve / Conditional / Reject** with ≤ 2-sentence rationale.

### Evidence completeness

For every scored dimension, the report must include **both a summary and the underlying
evidence**. Summary-only output (heatmaps, rollup tables, category-level scores) is
insufficient as a primary deliverable. Specifically:

- Requirements traceability: include the full matrix (one row per requirement ID, columns
  per implementation) alongside any category-level heatmap.
- Cyclomatic complexity: include per-function detail for all functions above the CC limit,
  not just aggregate counts.
- Output artifact quality: include all raw metric values alongside normalised scores.
- Any KPI category with a heatmap or summary table must also include the underlying data
  that produced it in the Appendix.

This matters because summary tables hide cross-cutting patterns that are only visible in
the full evidence — a requirement that every implementation failed is invisible in a
per-implementation score rollup, but immediately apparent in the full matrix.

### Multi-report consolidation

When an evaluation produces multiple separate report files (e.g., one per KPI group), also
produce a `report-manifest.json` listing all artifact paths, their sections, and the date
generated. This allows a later consolidation pass to know what exists and where without
manual reconstruction.

```json
{
  "evaluation_id": "string",
  "generated": "ISO-8601",
  "artifacts": [
    { "path": "FCG_K19_Requirements_Traceability.docx", "sections": ["traceability-matrix", "k-scores"] },
    { "path": "FCG_Cyclomatic_Complexity_Analysis.docx", "sections": ["ccn-summary", "fn-detail"] }
  ]
}
```

---

## Methodology Prompt

After completing the evaluation, produce a `methodology-prompt.md` file that captures the
evaluation approach so it can be rerun or adapted in the future. Generate this for every
evaluation as a standing output — not only when explicitly requested.

The file should contain:

1. **What was evaluated** — agent type, spec reference, implementations assessed
2. **Dimensions scored** — list each KPI or requirement category with its measurement method
3. **Tools and commands used** — exact tool names, versions, and command patterns
   (e.g., `lizard --CCN 10 --languages typescript src/` for cyclomatic complexity)
4. **Thresholds applied** — pass/fail criteria for each dimension
5. **A reusable prompt** — a self-contained prompt a future evaluator can give to Claude to
   reproduce the same evaluation on a new set of implementations

Format the reusable prompt as a fenced code block so it can be copy-pasted directly.

---

## Requirements Traceability Methodology

When evaluating compliance against a **requirements specification** (rather than scoring
an instruction set), use this approach instead of or alongside the standard KPI framework:

1. **Extract and count requirements**: Extract each discrete requirement from the spec and
   assign it a unique ID (e.g., A1, B3). Record the total requirement count before scoring.
2. **Verify coverage**: Confirm that every extracted requirement has a corresponding scored
   row. Compute a coverage percentage: `requirements_evaluated / total_requirements × 100`.
   If coverage < 100%, document which requirements were not evaluated and why. A coverage
   gap means the K-score may be optimistic — flag this in the report header as a Scope Limitation.
3. **Score each requirement**: PASS (1.0) / PARTIAL (0.5) / FAIL (0.0) per implementation.
4. **Produce the full traceability matrix**: rows = requirements (ID + short description),
   columns = implementations, cells = PASS / PARTIAL / FAIL. Include this full matrix in
   the report — do not substitute a category-level heatmap for it.
5. **Compute K-score**: `Σ(scores) / total_requirements × 10` (0–10 scale) per implementation.
6. **Identify universal failures**: After completing all scores, scan every row for
   requirements where every implementation received FAIL. These are cross-cutting gaps that
   typically indicate a spec ambiguity, a missing constraint, or a universally misunderstood
   requirement — not an individual implementation weakness. Elevate these to the Executive
   Summary as a distinct "Cross-Cutting Gaps" finding.
7. **Spec Improvement Recommendations**: For each universal failure, include an actionable
   rewrite with exact example spec language. These feed back into the specification before
   re-evaluation.

---

## KPI Framework

Read `references/kpis.md` for the complete KPI definitions, targets, and measurement methods.

Quick summary of Critical KPIs (2× weight in overall score):
- **FCR** — Functional Correctness Rate (code gen): ≥ 85%
- **SVR** — Security Vulnerability Rate (code gen): < 3%
- **TCR** — Task Completion Rate (automation): ≥ 90%
- **RBR** — Rollback Rate (automation): < 2%
- **ICS** — Internal Consistency Score (instruction quality): 100%
- **OVR** — Output Validity Rate (output-generative): ≥ 90%
- **ACS** — Aesthetic Composite Score (output-generative): ≥ 6.0 / 10

---

## Scoring Rubric

| Score | Label | Criteria |
|-------|-------|---------|
| 5 — Exemplary | At or above target | No action required |
| 4 — Proficient | Within 5 pp of target | Document; review at next cycle |
| 3 — Developing | 5–15 pp below target | Include improvement recommendations |
| 2 — Inadequate | 15–30 pp below target | Block deployment; escalate to tech lead |
| 1 — Critical Failure | >30 pp below target or safety issue | Do not deploy; notify stakeholders immediately |

### Overall Rating → Deployment Decision

| Score | Rating | Decision |
|-------|--------|---------|
| 4.5–5.0 | Exemplary | Approve for production |
| 3.5–4.4 | Proficient | Approve with minor action items |
| 2.5–3.4 | Developing | Conditional; resolve Inadequate KPIs first |
| 1.5–2.4 | Inadequate | Reject; remediation required |
| < 1.5 | Critical Failure | Reject; escalate immediately |

---

## Normative Rules Summary

**Evaluation Integrity**
- Fix random seeds, set temperature=0 for all generative calls
- Run evaluations in an isolated sandbox; no production system interaction
- Define ground truth before running test cases
- Minimum 30 test cases per domain (document exceptions)
- In version-sensitive contexts, note which version is under evaluation
- For output-generative agents: document the number of seeds used; flag single-seed evaluations as a scope limitation

**Truth Discipline**
- Prefer primary sources (see `references/sources.md`) for factual claims
- Fetch current version of any named standard before applying it; cite version and source
- If fetched source conflicts with training data, flag the conflict; do NOT resolve unilaterally

**Instruction Quality Assessment**
- Flag directives with undefined ambiguous qualifiers ("good", "reasonable", "appropriate")
- Check for the four structural completeness criteria: what to do, what never to do, edge-case handling, output format
- Report example coverage (positive and negative examples per core behavior)
- Check for internally contradictory directives
- Flag instruction sets lacking version number, revision date, or changelog

**Safety Constraints**
- Do NOT fabricate results or claim certainty without evidence
- Do NOT take external actions without explicit user permission (state action, provide dry-run preview, wait for approval)
- Do NOT modify, delete, or deploy any artifact produced by the evaluated agent
- Flag any finding where agent outputs could cause harm, expose sensitive data, or introduce security vulnerabilities — regardless of stated scope
- Verify task automation agents explicitly prohibit logging/exfiltrating sensitive data (absence = Critical finding)
- Assess least-privilege compliance for tool permissions (violation = Major finding)
- Safety findings take precedence over evaluation deadlines

---

## Evaluation Defaults

State these at Step 2 and ask for corrections:

| Parameter | Default |
|-----------|---------|
| Temperature | 0.0 |
| Seed | 42 |
| Min test cases / domain | 30 (≥20 typical, ≥5 edge, ≥3 adversarial, ≥2 regression) |
| Regression threshold | 5 pp delta flags as regression |
| Critical KPI weight | 2× (FCR, SVR, TCR, RBR, ICS, OVR, ACS) |
| SAST tool (Python) | Bandit (latest stable) |
| SAST tool (multi-language) | Semgrep (latest stable) |
| DC threshold | ≥ 70% |
| CC limit | ≤ 10 per function |
| Human rater involvement | ≥ 1 for FAR and CS |
| Report format | `.docx` (primary) + `.json` (summary) + `methodology-prompt.md` |
| Artifact retention | 90 days minimum, version-controlled |
| Follow-up trigger | After any revision addressing Critical or Major finding |
| Seed count (output-generative) | ≥ 20 for full eval; document if fewer used |
| Composite weight (output-generative) | 55% Technical + 45% Aesthetic (document deviations) |
| Requirements coverage | 100% (document any gap with justification) |
| Multi-report manifest | `report-manifest.json` when > 1 output file produced |

---

## Reference Files

- `references/kpis.md` — Full KPI definitions with targets and measurement methods (read when scoring KPIs)
- `references/report-template.md` — Complete report structure and field requirements (read when generating report)
- `references/sources.md` — Authoritative documentation sources to fetch before applying named standards
