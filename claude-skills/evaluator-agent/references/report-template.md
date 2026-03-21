# Report Output Template

All evaluation reports must adhere to this structure. Deviations must be justified in the
report header. Deliver as `.docx` (human-readable), `.json` (machine-readable summary), and
`methodology-prompt.md` (reusable evaluation prompt — see SKILL.md § Methodology Prompt).

Sections 7.8 and 7.9 are **conditional** — include them only when applicable (see each
section header for the trigger condition).

**Generation order**: produce sections 7.1 and 7.3–7.9 before writing 7.2. The Executive
Summary must reflect findings from the complete evidence record, not a preliminary read.

---

## 7.1 Report Header (Required Fields)

- **Agent Name and ID**
- **Instruction Set Version**
- **Evaluation Date** (ISO 8601)
- **Evaluator Agent Version**
- **Triggering Engineer** (name and role)
- **Evaluation Scope** — Full / Targeted / Instruction-only; list excluded KPIs with justification
- **Overall Score** — weighted average and rating tier (per §6.1 in SKILL.md)
- **Deployment Decision** — Approve / Conditional / Reject with ≤ 2-sentence rationale
- **Scope Limitations** — document any deviations from defaults (e.g., single-seed evaluation, fewer than 30 test cases)

---

## 7.2 Executive Summary

- ≤ 200 words
- **Write this section last** — after all evidence sections (7.3–7.9) are complete.
- Before drafting, scan all scored dimensions for universal verdicts (PASS, PARTIAL, or FAIL
  across every implementation). Universal FAILs indicate a spec gap or missing constraint and
  must be called out explicitly — they are not implementation weaknesses.
- Summarize: strengths, critical findings (including any cross-cutting gaps), recommended
  next actions
- Must be comprehensible to a non-technical stakeholder

---

## 7.3 KPI Scorecard

A table listing every applicable KPI grouped by category (Code Gen / Task Automation / Instruction Quality / Output Artifact Quality):

| KPI | Raw Value | Target | Score (1–5) | Status |
|-----|-----------|--------|-------------|--------|
| FCR [C] | xx% | ≥ 85% | 5 | Pass |
| ... | | | | |

Use `N/A` only when scope explicitly excludes a dimension; document in the report header.

---

## 7.4 Instruction Quality Findings

Structured findings log. Each entry:

```
Finding ID: IQ-001
Severity: Critical | Major | Minor
Location: [Section and line reference in instruction document]
Description: [Clear description of the issue]
Recommendation: [Specific rewrite — REQUIRED for Critical and Major]
```

---

## 7.5 Performance Findings

Structured findings log. Each failure or warning:

```
Finding ID: PF-001
KPI Affected: [KPI name]
Failing Test Case(s): [Case ID and input]
Actual Output: [What the agent produced]
Expected Output: [Ground truth]
Root Cause Hypothesis: [Best explanation]
Recommended Fix: [Specific action]
```

---

## 7.6 Baseline Comparison

Include only when a prior version exists; otherwise document the omission with one sentence.

Delta table format:

| KPI | Prior Version | Current Version | Delta | Status |
|-----|--------------|----------------|-------|--------|
| FCR | 82% | 87% | +5 pp | Improvement |
| ... | | | | |

Regressions (> 5 pp decline) must be highlighted.

---

## 7.7 Appendix

- Full test suite: inputs, expected outputs, actual outputs, pass/fail
- Raw KPI computation data — every scored dimension must include its raw source data here,
  even if a summary table appears earlier in the report
- Evaluator model configuration: model ID, temperature, seed, timestamp
- Human rater notes (if any)
- SAST tool findings (name, version, output)
- **Seed log** *(output-generative agents)*: list all seeds used; if only one seed was used, note this explicitly as a scope limitation with a recommendation to re-evaluate across ≥ 20 seeds
- **Report manifest** *(when multiple output files were produced)*: list all artifact paths,
  their sections, and generation date (see SKILL.md § Multi-report consolidation for schema)

---

## 7.8 Output Artifact Quality *(include when agent type = output-generative)*

### 7.8.1 Metric Definitions

A table explaining each metric with its formula, units, and interpretation. Readers should
be able to understand what each score means without consulting the KPI reference.

### 7.8.2 Technical Quality Scores

Table: one row per implementation (if multiple), columns = each technical KPI (CFS, VCI,
ED, PCR), plus normalised score (0–10) and overall Technical Quality Score.

### 7.8.3 Aesthetic Quality Scores

Table: one row per implementation, columns = each aesthetic sub-metric (GS, CHS, ΔE, PC,
AB), normalised sub-scores, and Aesthetic Composite Score (ACS).

### 7.8.4 Combined Score

Table: one row per implementation, columns = Technical Quality Score, ACS, Combined Score,
composite weights used, and ranking. Include a **Rank Shift** column showing movement from
the Technical-only ranking to the Combined ranking — this highlights the practical impact
of including aesthetic quality on the final decision.

### 7.8.5 Interpretation

Narrative (≤ 150 words per implementation or group) explaining *why* the scores look the
way they do. Explain the relationship between subjective user perception and the objective
metrics — for example, note if an implementation that users find visually appealing scores
high on ΔE and CHS, or if an implementation that looks "harsh" scores low on GS.

---

## 7.9 Multi-Implementation Comparative Ranking *(include when ≥ 2 implementations evaluated)*

### 7.9.1 Overall Ranking Table

| Rank | Implementation | Overall Score | Code/Task KPIs | Artifact Quality | Instruction Quality |
|------|---------------|--------------|----------------|-----------------|---------------------|
| 1 | ... | 8.4 | 8.7 | 9.2 | 7.8 |
| ... | | | | | |

### 7.9.2 Requirements Traceability Matrix *(include when spec compliance is assessed)*

A full matrix: rows = individual requirements (by ID and short description), columns =
implementations, cells = PASS / PARTIAL / FAIL with score value. Final row = K-score per
implementation.

Include a coverage line above the matrix: `Requirements evaluated: N / total (XX%)`. If
coverage < 100%, list unscored requirement IDs and the reason.

A category-level heatmap may appear alongside the full matrix for readability, but does not
replace it. The full row-by-row matrix is the primary evidence artifact.

Highlight universal failures (FAIL across all implementations) in a separate callout
immediately after the matrix. These must also appear in the Executive Summary (7.2).

### 7.9.3 Complementary Strengths Analysis

A brief table or narrative identifying where different implementations excel in different
dimensions. For example: "Implementation A leads on technical correctness; Implementation B
leads on aesthetic quality; no single implementation dominates across all dimensions."
This section helps the reader understand tradeoffs rather than treating the ranking as
a single authoritative verdict.

### 7.9.4 Spec Improvement Recommendations *(include when universal failures are identified)*

For each requirement that failed across all implementations, provide:
- The requirement ID and current spec text
- The root cause (ambiguous language, missing constraint, under-specified behaviour)
- A concrete rewrite suggestion with example spec language

These findings should be fed back into the specification before re-evaluation.

---

## JSON Summary Schema

```json
{
  "agent_id": "string",
  "instruction_version": "string",
  "evaluation_date": "ISO-8601",
  "evaluator_model": "string",
  "triggering_engineer": "string",
  "scope": "full | targeted | instruction-only",
  "agent_types": ["code_gen", "task_automation", "output_generative"],
  "implementation_count": 1,
  "overall_score": 0.0,
  "overall_rating": "Exemplary | Proficient | Developing | Inadequate | Critical Failure",
  "deployment_decision": "Approve | Conditional | Reject",
  "deployment_rationale": "string (≤ 2 sentences)",
  "scope_limitations": ["string"],
  "kpis": [
    {
      "name": "string",
      "category": "code_gen | task_automation | instruction_quality | output_artifact_quality",
      "critical": true,
      "raw_value": "string",
      "target": "string",
      "score": 1,
      "status": "Pass | Warning | Fail"
    }
  ],
  "findings": [
    {
      "id": "IQ-001 | PF-001",
      "type": "instruction_quality | performance | spec_improvement",
      "severity": "Critical | Major | Minor",
      "description": "string",
      "recommendation": "string"
    }
  ],
  "comparative_ranking": [
    {
      "implementation": "string",
      "overall_score": 0.0,
      "technical_score": 0.0,
      "aesthetic_score": 0.0,
      "k_score": 0.0,
      "rank": 1
    }
  ],
  "seeds_used": 1,
  "composite_weights": {"technical": 0.55, "aesthetic": 0.45},
  "baseline_comparison_available": true,
  "artifacts_committed": true,
  "requirements_coverage_pct": 100,
  "universal_failures": ["requirement-id-1", "requirement-id-2"],
  "methodology_prompt_path": "methodology-prompt.md",
  "report_manifest_path": "report-manifest.json"
}
```
