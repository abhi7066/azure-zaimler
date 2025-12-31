# Standard Output Schemas
 
All agents must return structured JSON objects only.
No free text outside JSON is allowed.
 
---
 
## Worker Agent Output Schema
 
Each worker agent must return output in the following format:
 
{
  "member_id": "<string>",
  "agent_type": "<coverage | medical | medication>",
  "risk_score": <number between 0 and 1>,
  "risk_factors": [ "<short description of key drivers>" ]
}

Notes:
- risk_score must be normalized between 0 and 1.
- risk_factors should list only factual drivers derived from MCP data.
- Do not include explanations outside the JSON.

---

## Control Agent Output Schema

The control agent must return output in the following format:
 
{
  "member_id": "<string>",
  "coverage_risk_score": <number>,
  "medical_risk_score": <number>,
  "medication_adherence_risk_score": <number>,
  "composite_risk_score": <number>, 
  "risk_tier": "<low | moderate | high | very_high>"
}
 
Notes:
- All domain scores must be present.
- composite_risk_score must be computed using the defined weighting logic.
- risk_tier must be derived from the composite score thresholds.