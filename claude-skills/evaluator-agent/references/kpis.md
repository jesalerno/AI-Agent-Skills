# KPI Framework Reference

Full KPI definitions, targets, and measurement methods for the Evaluator Agent.

Critical KPIs are marked **[C]** and carry 2× weight in the overall weighted score.

---

## 5.1 Code Generation Agent KPIs

| KPI | Definition | Target | Measurement |
|-----|-----------|--------|-------------|
| Functional Correctness Rate (FCR) **[C]** | % of generated code samples passing all unit tests (pass@1) | ≥ 85% | Automated test runner; report pass@1 and pass@5 |
| Syntax Error Rate (SER) | % of outputs containing unparseable code | < 2% | Static AST parser (e.g., `ast.parse` for Python) |
| Instruction Adherence Rate (IAR) | % of outputs correctly implementing all explicit prompt constraints | ≥ 90% | LLM-as-judge scoring against constraint checklist |
| Hallucination Rate (HR) | % of outputs referencing non-existent libraries, functions, or APIs | < 5% | Automated import resolution check + manual spot-check |
| Security Vulnerability Rate (SVR) **[C]** | % of outputs containing ≥ 1 OWASP Top 10 vulnerability | < 3% | SAST tool (Bandit/Semgrep); report tool name and version |
| Cyclomatic Complexity (CC) | Average cyclomatic complexity per function across all outputs | ≤ 10 | `radon cc` (Python) or language-equivalent |
| Documentation Coverage (DC) | % of public functions/methods with docstrings or inline comments | ≥ 70% | Static analysis (`pydocstyle`, `pylint`) |
| Token Efficiency Score (TES) | Ratio of minimum viable tokens to actual output tokens | ≥ 0.6 | Token count vs. reference solution length |

---

## 5.2 Task Automation Agent KPIs

| KPI | Definition | Target | Measurement |
|-----|-----------|--------|-------------|
| Task Completion Rate (TCR) **[C]** | % of tasks completed successfully end-to-end without human intervention | ≥ 90% | Automated outcome validation against expected end state |
| Step Accuracy Rate (SAR) | % of individual steps executed correctly across all evaluated tasks | ≥ 95% | Step-by-step log comparison against reference trajectory |
| Error Recovery Rate (ERR) | % of recoverable errors from which agent self-recovers without human input | ≥ 80% | Inject known error states; measure autonomous recovery |
| Efficiency Score (ES) | Actual steps ÷ optimal steps (1.0 = optimal; lower is better) | ≤ 1.3 | Trajectory analysis vs. expert-curated reference paths |
| Tool Call Accuracy (TCA) | % of tool/function calls with correct parameters and correct sequence | ≥ 92% | Automated schema validation of all tool call logs |
| False Action Rate (FAR) | % of steps taken on incorrect assumptions | < 5% | Human rater review of ambiguous-input scenarios |
| Latency (P95) | 95th-percentile end-to-end task completion time | Set per task family | Timestamped execution logs |
| Rollback Rate (RBR) **[C]** | % of tasks requiring human rollback after agent completion | < 2% | Post-execution audit log review |

---

## 5.3 Instruction Quality KPIs

| KPI | Definition | Target | Measurement |
|-----|-----------|--------|-------------|
| Clarity Score (CS) | % of directives free of undefined ambiguous qualifiers | ≥ 90% | NLP ambiguity scanner + human spot-check |
| Completeness Score (COS) | % of required structural sections present (role, dos, don'ts, output format, examples) | 100% | Structural checklist against template |
| Example Coverage (EC) | % of documented core behaviors with ≥ 1 positive and ≥ 1 negative example | ≥ 80% | Manual checklist per behavior |
| Internal Consistency Score (ICS) **[C]** | % of directive pairs with no logical contradiction | 100% | LLM-as-judge pairwise consistency check |
| Specificity Score (SS) | Ratio of quantitative/measurable criteria to total criteria | ≥ 0.7 | Regex + LLM classification of criteria type |
| Version Control Compliance (VCC) | Presence of version number, revision date, and changelog | Pass / Fail | Structural check |

---

## 5.4 Output Artifact Quality KPIs (output-generative agents)

Use these KPIs when the agent under evaluation produces visual, audio, or other media
artifacts. Compute technical and aesthetic sub-scores separately, then combine into a
weighted composite (default: 55% technical / 45% aesthetic — document any deviation).

> **Implementation note**: Use Python with NumPy, Pillow, scikit-image, SciPy, and
> scikit-learn for all metric computation. Install with:
> `pip install numpy pillow scikit-image scipy scikit-learn --break-system-packages`

### Technical Quality Metrics

| KPI | Definition | Target | Measurement |
|-----|-----------|--------|-------------|
| Output Validity Rate (OVR) **[C]** | % of generated outputs meeting minimum quality threshold (non-blank, correct format, within expected dimensions) | ≥ 90% | Automated format and content checks |
| Colorfulness Score (CFS) | Mean colorfulness using the Hasler-Süsstrunk (2003) method: `std(rg) + 0.3 × mean(yb)` where rg = R−G, yb = 0.5(R+G)−B | ≥ 30 (colour outputs); flag if 0 (greyscale) | NumPy / PIL per-image computation |
| Visual Complexity Index (VCI) | Composite of Shannon entropy (pixel intensity histogram) and box-counting fractal dimension | Entropy ≥ 3.5; fractal dim ≥ 1.3 | scikit-image entropy; box-counting algorithm |
| Edge Density (ED) | Mean Sobel edge magnitude normalised to [0, 1] | ≥ 0.05 (for detail-rich outputs) | scikit-image `sobel` filter |
| Pixel Coverage Rate (PCR) | % of pixels with non-background colour (useful for sparse outputs) | Domain-specific | Threshold on dominant background colour |

### Aesthetic Quality Metrics

| KPI | Definition | Target | Measurement |
|-----|-----------|--------|-------------|
| Aesthetic Composite Score (ACS) **[C]** | Weighted combination of the five sub-metrics below (equal weights unless domain-specific calibration is available) | ≥ 6.0 / 10 | Python KMeans + NumPy + scikit-image |
| Gradient Smoothness (GS) | % of adjacent pixel pairs with Sobel magnitude < 0.02 threshold; higher = smoother gradients | ≥ 0.50 | scikit-image Sobel per pixel pair |
| Colour Harmony Score (CHS) | Alignment of dominant palette hues (K-means, k=5) against standard harmony templates: complementary (180°), analogous (30°/60°), triadic (120°/240°), tetradic (90°), split-complementary (150°/210°) | ≥ 0.70 | KMeans on HSV hues; cosine similarity to templates |
| Perceptual Smoothness (ΔE) | Mean CIELAB ΔE between adjacent pixels; lower = perceptually smoother (1.0 ≈ just-noticeable difference threshold) | ≤ 2.0 (silky); flag if > 5.0 (harsh) | Convert RGB→Lab; Euclidean distance on adjacent pairs |
| Palette Cohesion (PC) | Circular standard deviation of dominant hues; lower = more coherent palette | ≤ 60° circular std dev | SciPy circular statistics on KMeans hue centroids |
| Aesthetic Balance (AB) | Berlyne (1971) arousal theory Goldilocks score: penalises both too-simple (entropy < 2.5) and too-chaotic (entropy > 5.5) outputs on a normalised 0–10 scale | ≥ 6.0 | Piecewise linear function on Shannon entropy |

### Composite Scoring

Compute the Aesthetic Composite Score (ACS) by normalising each sub-metric to [0, 10]
and taking the mean. To compute Technical Quality Score, normalise CFS, VCI, ED, and
PCR to [0, 10] and take the mean. Then combine:

```
Combined Score = (Technical Quality Score × 0.55) + (ACS × 0.45)
```

Report all raw values, normalised values, and the combined score. When evaluating multiple
implementations, rank by Combined Score and include a shift column showing movement from
the Technical-only ranking to highlight the aesthetic impact on the final ranking.

### Seed Stability Rate (SSR)

When ≥ 5 seeds are sampled: compute the coefficient of variation (CV = std/mean) of the
Combined Score across seeds per implementation. Lower CV = more consistent quality.

| CV | Stability |
|----|-----------|
| < 0.10 | High — reliable across seeds |
| 0.10–0.25 | Moderate — note in report |
| > 0.25 | Low — flag as Major finding |

---

## Scoring Computation

**Weighted average formula:**
- Critical KPIs: weight = 2
- Standard KPIs: weight = 1
- Overall = Σ(score × weight) / Σ(weights)

Only include KPIs relevant to the agent type(s) under evaluation. Document excluded KPIs
with justification (e.g., "OVR and ACS not applicable — code generation agent only").

**Score tier assignment:**
| Tier | Label | Delta from Target |
|------|-------|------------------|
| 5 | Exemplary | At or above target |
| 4 | Proficient | Within 5 pp below target |
| 3 | Developing | 5–15 pp below target |
| 2 | Inadequate | 15–30 pp below target |
| 1 | Critical Failure | > 30 pp below target, or any safety issue |
