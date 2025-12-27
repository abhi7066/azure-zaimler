# Medication Adherence Risk Knowledge Document

## 1. Purpose
This document defines how to calculate medication adherence risk
based on days supply and treatment duration.

The output is a normalized adherence risk score between 0 and 1.

## 2. Inputs (per medication)
- EarliestFillDate: date
- LatestFillDate: date
- TotalDaysSupply: integer

## 3. Assumptions
- Duration must be at least 1 day
- Adherence ratio is capped at 1
- Missing supply defaults to 0

## 4. Calculation Logic

### Step 1: Calculate Duration
DurationDays =
(LatestFillDate - EarliestFillDate) + 1

### Step 2: Calculate Adherence Ratio
AdherenceRatio =
min(TotalDaysSupply / DurationDays, 1)

### Step 3: Per-Medication Risk Score
MedicationRiskScore = 1 - AdherenceRatio

### Step 4: Overall Adherence Risk
OverallRiskScore =
Weighted average of MedicationRiskScore
(weighted by DurationDays)

## 5. Thresholds & Interpretation
- < 0.10 → Very low risk
- 0.10–0.30 → Mild risk
- 0.30–0.60 → Moderate risk
- > 0.60 → High adherence risk

## 6. Output Schema
{
  "pharmacy_risk_score": number,
  "pharmacy_risk_level": "low | moderate | high",
  "pharmacy_risk_flags": string[]
}

## 7. Example
Medication:
Duration = 60 days
TotalDaysSupply = 36 days

AdherenceRatio = 0.6
MedicationRiskScore = 0.4

Output:
{
  "pharmacy_risk_score": 0.4,
  "pharmacy_risk_level": "moderate",
  "pharmacy_risk_flags": ["medication_non_adherence"]
}
