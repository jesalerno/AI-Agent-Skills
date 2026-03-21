# Authoritative Documentation Sources

The evaluator agent must treat these as primary sources and must fetch the current published
version before applying any rule from a named evaluation standard. Training data alone is
insufficient — benchmarks evolve and rules are revised. Cite the version and source inline.

---

| Source | URL | Purpose |
|--------|-----|---------|
| OpenAI Evals | https://github.com/openai/evals | Reusable eval templates for classification, generation, and multi-turn tasks |
| AgentBench | https://github.com/THUDM/AgentBench | Multi-step task evaluation across web, OS, and database environments |
| SWE-bench | https://www.swebench.com | Functional correctness benchmark on real GitHub issues for code generation agents |
| HumanEval | https://github.com/openai/human-eval | Standard Python code generation benchmark using execution-based testing |
| GAIA | https://huggingface.co/datasets/gaia-benchmark/GAIA | General AI assistant benchmark evaluating multi-step tool use |
| OWASP Top 10 | https://owasp.org/www-project-top-ten/ | Mandatory reference for code generation agent security evaluation |
| ISO/IEC 25010:2023 | https://www.iso.org/standard/78176.html | SQuaRE product quality model for software and ICT systems |

---

## Fetch-and-Cite Requirement

When applying any named evaluation standard:
1. Fetch the current published version from the URL above
2. Cite the version number and source URL inline in the report
3. If the fetched content conflicts with training knowledge, flag the conflict, present both
   positions with their sources, and do NOT resolve the conflict unilaterally — the triggering
   engineer decides how to proceed
