# Coverage Risk Knowledge Document

## 1. Purpose
This document defines how to calculate coverage-related risk for a member
based on coverage status, gender, age, and coverage continuity.

The output is a normalized coverage risk score between 0 and 1.

## 2. Inputs
- CoverageStatus: string ("A" = Active, "T" = Terminated)
- Gender: string ("M" or "F")
- Age: integer (0–100)
- CoverageMonths: integer (0–12)

## 3. Assumptions
- Age values greater than 100 are capped at 100
- CoverageMonths must be between 0 and 12
- Missing values default to the lowest-risk value
- Risk score is deterministic and explainable

## 4. Calculation Logic

### Step 1: Assign Status Weight
If CoverageStatus == "A" → StatusWeight = 0.2  
If CoverageStatus == "T" → StatusWeight = 0.5  

### Step 2: Assign Gender Weight
If Gender == "M" → GenderWeight = 0.3  
If Gender == "F" → GenderWeight = 0.2  

### Step 3: Normalize Age
AgeScore = Age / 100  

### Step 4: Normalize Coverage Months
CoverageScore = (12 - CoverageMonths) / 12  

### Step 5: Final Coverage Risk Score
CoverageRiskScore =
0.3247 * StatusWeight +
0.1948 * GenderWeight +
0.5195 * AgeScore +
0.2597 * CoverageScore

## 5. Thresholds & Interpretation
- < 0.40 → Low coverage risk
- 0.40–0.70 → Moderate coverage risk
- > 0.70 → High coverage risk

## 6. Output Schema
{
  "coverage_risk_score": number,
  "coverage_risk_level": "low | moderate | high",
  "coverage_risk_flags": string[]
}

## 7. Example
Input:
CoverageStatus = "T"
Gender = "M"
Age = 62
CoverageMonths = 4

Calculation:
AgeScore = 0.62
CoverageScore = 0.6667

CoverageRiskScore ≈ 0.71

Output:
{
  "coverage_risk_score": 0.71,
  "coverage_risk_level": "high",
  "coverage_risk_flags": ["coverage_termination_risk"]
}
