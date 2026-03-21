# JSON Output Schema

The Phase 2 output MUST conform to this schema. All fields marked **required** must be present
and non-null. Fields marked **optional** may be omitted when not applicable.

Produce valid JSON — no `//` comments, no trailing commas, no undefined values. Validate the
structure before presenting the output and fix any errors before delivering.

After the JSON block, deliver the summary table from the SKILL.md Phase 2 output template.

## ID assignment rules

Assign IDs sequentially; never reset within a nested scope.

| Prefix | Scope |
|---|---|
| `REQ-001`, `REQ-002`, … | requirements register |
| `SH-001`, `SH-002`, … | stakeholder register |
| `RISK-001`, `RISK-002`, … | risk register |
| `NFR-001`, `NFR-002`, … | non-functional requirements |
| `OQ-001`, `OQ-002`, … | open questions |
| `EPIC-001`, `EPIC-002`, … | epics |
| `STORY-001`, `STORY-002`, … | stories (across all epics) |
| `TASK-001`, `TASK-002`, … | tasks (across all stories in all epics) |
| `AC-001`, `AC-002`, … | acceptance criteria (reset per story) |

## Full schema

```json
{
  "spec_version": "1.5",
  "spec_revision": "1.0.0",
  "generated_at": "<ISO-8601 timestamp>",
  "title": "<project or feature name>",
  "summary": "<2–4 sentence description of what is being built>",

  "success_criteria": ["<observable outcome 1>", "<observable outcome 2>"],

  "technical_approach": {
    "description": "<1–3 paragraph high-level architecture summary>",
    "key_decisions": [
      {
        "decision": "<the choice made>",
        "rationale": "<why this choice>",
        "trade_offs": "<what is given up or risked>"
      }
    ],
    "architecture_notes": "<optional: data flow, component boundaries, integration patterns>"
  },

  "not_included": ["<explicit out-of-scope item>"],
  "assumptions": ["<assumption made during decomposition>"],

  "change_log": [],

  "stakeholder_register": [
    {
      "id": "SH-001",
      "name": "<role or organisation name>",
      "interest": "<what this stakeholder needs from the system>",
      "review_required": true
    }
  ],

  "open_questions": [
    {
      "id": "OQ-001",
      "question": "<the unresolved question>",
      "impact": "<what changes if answered differently>",
      "blocking": true
    }
  ],

  "requirements_register": [
    {
      "id": "REQ-001",
      "source_text": "<verbatim from source — MUST NOT be reworded>",
      "source_location": "<optional: section or line reference>",
      "type": "functional",
      "rationale": "<why this requirement exists>",
      "stakeholder_source": "SH-001",
      "criticality": "business_critical",
      "safety_integrity_level": null,
      "status": "proposed",
      "verification_method": "test",
      "verification_criterion": "<measurable, observable condition that demonstrates this requirement is satisfied>",
      "implemented_by": ["STORY-001"],
      "change_history": []
    }
  ],

  "traceability_matrix": {
    "requirement_to_stories": [
      { "req_id": "REQ-001", "story_ids": ["STORY-001"] }
    ],
    "story_to_requirements": [
      { "story_id": "STORY-001", "req_ids": ["REQ-001"] }
    ],
    "coverage_gaps": []
  },

  "risk_register": [
    {
      "id": "RISK-001",
      "title": "<short title>",
      "category": "technical",
      "description": "<full description>",
      "likelihood": "medium",
      "impact": "high",
      "risk_level": "high",
      "mitigation": "<action to reduce likelihood or impact; or explicit acceptance rationale>",
      "owner": "<optional: role responsible>",
      "related_reqs": ["REQ-001"]
    }
  ],

  "nfrs": [
    {
      "id": "NFR-001",
      "title": "<short title>",
      "nfr_category": "<category from nfr-checklist.md>",
      "description": "<full description>",
      "acceptance_criterion": "<measurable condition>",
      "priority": "must_have",
      "implemented_by": ["STORY-001"]
    }
  ],

  "threat_model": {
    "stride_analysis": [
      {
        "threat_category": "Spoofing",
        "description": "<specific threat in this system's context>",
        "likelihood": "medium",
        "impact": "high",
        "risk_level": "high",
        "mitigated_by": ["TASK-001"]
      }
    ]
  },

  "epics": [
    {
      "id": "EPIC-001",
      "title": "<5 words or fewer>",
      "goal": "<one sentence — business or user outcome>",
      "priority": "must_have",
      "story_ids": ["STORY-001"],
      "stories": [
        {
          "id": "STORY-001",
          "epic_ref": "EPIC-001",
          "title": "<8 words or fewer>",
          "actor": "<named role or system>",
          "description": "As a <actor>, I want <capability> so that <benefit>.",
          "priority": "must_have",
          "source_refs": ["REQ-001"],
          "nfr_refs": ["NFR-001"],
          "acceptance_criteria": [
            {
              "id": "AC-001",
              "given": "<context>",
              "when": "<action>",
              "then": "<observable outcome>"
            }
          ],
          "security_notes": "<optional: present when Story touches security concerns>",
          "notes": "<optional: required when priority is wont_have>",
          "task_ids": ["TASK-001"],
          "tasks": [
            {
              "id": "TASK-001",
              "story_ref": "STORY-001",
              "title": "<clear deliverable statement>",
              "description": "<full description>",
              "type": "dev",
              "complexity": "medium",
              "estimate_points": 3,
              "dependencies": [],
              "threat_refs": []
            }
          ]
        }
      ]
    }
  ]
}
```

## Valid enum values

| Field | Valid values |
|---|---|
| `type` (requirement) | `functional`, `non_functional`, `constraint`, `assumption` |
| `criticality` | `safety_critical`, `mission_critical`, `business_critical`, `standard` |
| `status` (requirement) | `proposed`, `under_discussion`, `agreed`, `rejected`, `baseline` |
| `verification_method` | `test`, `demonstration`, `analysis`, `inspection` |
| `priority` (epic/story/nfr) | `must_have`, `should_have`, `could_have`, `wont_have` |
| `task.type` | `dev`, `test`, `security`, `infra`, `docs` |
| `complexity` | `small`, `medium`, `large` |
| `estimate_points` | `1`, `2`, `3`, `5`, `8`, `13` |
| `likelihood` / `impact` | `high`, `medium`, `low` |
| `risk_level` | `critical`, `high`, `medium`, `low` |
| `risk.category` | `technical`, `organizational`, `schedule`, `external` |
| `threat_category` | `Spoofing`, `Tampering`, `Repudiation`, `Information Disclosure`, `Denial of Service`, `Elevation of Privilege` |
