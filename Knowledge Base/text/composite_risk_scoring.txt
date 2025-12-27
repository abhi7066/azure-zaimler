# Composite Risk Scoring Knowledge Document

## 1. Purpose
This document defines how to combine coverage, medical,
and pharmacy risk scores into a single composite risk score
and determine the care recommendation.

## 2. Inputs
- CoverageRiskScore (0–1)
- MedicalRiskScore (0–1)
- PharmacyRiskScore (0–1)

## 3. Assumptions
- All domain scores must be normalized between 0 and 1
- Missing domain scores default to 0

## 4. Calculation Logic

CompositeRiskScore =
0.50 * MedicalRiskScore +
0.30 * PharmacyRiskScore +
0.20 * CoverageRiskScore

## 5. Risk Tiers & Programs
- ≥ 0.80 → Complex Chronic Care Program
- 0.60–0.79 → High Risk Care Management
- 0.40–0.59 → Moderate Risk Tele-Coaching
- < 0.40 → Self-Management Resources

## 6. Output Schema
{
  "member_id": string,
  "composite_risk_score": number,
  "risk_tier": "low | moderate | high | very_high",
  "recommended_program": string,
  "explanation": string[]
}

## 7. Example
Inputs:
Medical = 0.78
Pharmacy = 0.70
Coverage = 0.65

CompositeRiskScore =
0.5*0.78 + 0.3*0.70 + 0.2*0.65 = 0.74

Output:
{
  "member_id": "12345",
  "composite_risk_score": 0.74,
  "risk_tier": "high",
  "recommended_program": "high_risk_care_management",
  "explanation": [
    "High medical utilization",
    "Medication non-adherence",
    "Coverage instability"
  ]
}
