# Medical Conditions & Utilization Risk Knowledge Document

## 1. Purpose
This document defines how to calculate medical utilization risk for a member
based on diagnosed conditions, inpatient admissions, and total allowed amount.

The output is a normalized medical risk score between 0 and 1.

## 2. Inputs
- Conditions: integer (number of distinct diagnoses)
- IP_Visits: integer (inpatient admissions in last 12 months)
- AllowedAmt: number (total allowed medical cost in USD)

## 3. Assumptions
- Conditions are capped at 10
- IP_Visits are capped at 5
- AllowedAmt is capped at 50,000 USD
- Missing values default to 0

## 4. Calculation Logic

### Step 1: Normalize Conditions
ConditionsScore = min(Conditions / 10, 1)

### Step 2: Normalize Inpatient Visits
IPScore = min(IP_Visits / 5, 1)

### Step 3: Normalize Cost
CostScore = min(AllowedAmt / 50000, 1)

### Step 4: Final Medical Risk Score
MedicalRiskScore =
0.30 * ConditionsScore +
0.35 * IPScore +
0.35 * CostScore

## 5. Thresholds & Interpretation
- 0.00–0.30 → Low medical risk
- 0.30–0.60 → Moderate medical risk
- 0.60–1.00 → High medical risk

## 6. Output Schema
{
  "medical_risk_score": number,
  "medical_risk_level": "low | moderate | high",
  "medical_risk_flags": string[]
}

## 7. Example
Input:
Conditions = 6
IP_Visits = 2
AllowedAmt = 30000

Scores:
ConditionsScore = 0.6
IPScore = 0.4
CostScore = 0.6

MedicalRiskScore = 0.53

Output:
{
  "medical_risk_score": 0.53,
  "medical_risk_level": "moderate",
  "medical_risk_flags": ["chronic_multi_morbidity"]
}
