---
id: HS-001
title: 'Regulatory Submission with Incomplete Traceability Matrix'
status: draft
links:
  - type: derives-from
    target: RISK-SW-006
  - type: derives-from
    target: HARM-001
---

# Regulatory Submission with Incomplete Traceability Matrix

## 1. Purpose

This document describes the hazardous situation in which an organization submits regulatory documentation (e.g., 510(k), CE Technical File) that contains an incomplete or inaccurate traceability matrix. This situation arises from the software risks identified in RISK-SW-006 (Incorrect Traceability) and contributes to the harm described in HARM-001 (Regulatory Non-Compliance).

## 2. Risk Category

| Field       | Value                                             |
| ----------- | ------------------------------------------------- |
| Category    | Hazardous Situation                               |
| Subcategory | Regulatory submission — traceability completeness |
| Standard    | ISO 14971, IEC 62304 §5.1.1, ISO 13485 §7.3.3     |

## 3. Hazard Identification

### 3.1 Hazard Description

An organization prepares a regulatory submission relying on the PactoSigna traceability matrix. Due to incorrect or incomplete traceability data (broken links, stale references, false coverage percentages), the submission contains a traceability matrix with undiscovered gaps. The regulatory body identifies these gaps during review.

### 3.2 Foreseeable Sequence of Events

1. Organization uses PactoSigna traceability matrix for regulatory submission preparation
2. The traceability matrix contains gaps that the automated gap detection failed to identify (per RISK-SW-006)
3. The organization exports and includes the traceability matrix in their regulatory submission
4. The regulatory body (FDA, Notified Body) reviews the submission and identifies traceability gaps
5. Submission is rejected or deficiency letter issued, delaying market authorization
6. Organization must remediate traceability and resubmit, incurring significant time and cost

### 3.3 Affected Users / Stakeholders

- Regulatory Specialists who prepared the submission
- QA Managers responsible for submission quality
- The organization facing delayed market access
- Patients who may benefit from the medical device but face delayed availability

## 4. Risk Estimation

### Before Mitigation

| Factor         | Rating             | Justification                                                                                        |
| -------------- | ------------------ | ---------------------------------------------------------------------------------------------------- |
| Severity       | 4 — Major          | Submission rejection causes significant delay, cost, and potential loss of market opportunity        |
| Probability    | 3 — Occasional     | Traceability gaps are common in manual processes; automated systems reduce but do not eliminate risk |
| Detectability  | 3 — Moderate       | Gaps in a large traceability matrix are difficult to spot during manual review                       |
| **Risk Level** | **High (RPN: 36)** | 4 × 3 × 3                                                                                            |

### After Mitigation

| Factor            | Rating              | Justification                                                                                    |
| ----------------- | ------------------- | ------------------------------------------------------------------------------------------------ |
| Severity          | 4 — Major           | Severity of rejected submission remains inherently high                                          |
| Probability       | 2 — Unlikely        | Automated gap detection, coverage percentages, and process controls reduce likelihood            |
| Detectability     | 1 — High            | Gap detection report explicitly shows missing links; CSV export enables independent verification |
| **Residual Risk** | **Medium (RPN: 8)** | 4 × 2 × 1                                                                                        |

## 5. Risk Mitigation

| #   | Mitigation Measure                                                      | Type    | Target ID | Status      |
| --- | ----------------------------------------------------------------------- | ------- | --------- | ----------- |
| 1   | Automated gap detection across all standard traceability patterns       | Design  | PRS-021    | Planned     |
| 2   | Coverage percentage calculations for each traceability pair             | Design  | PRS-021    | Planned     |
| 3   | Visual gap highlighting in traceability matrix view                     | Design  | PRS-021    | Planned     |
| 4   | CSV export for independent verification                                 | Design  | PRS-021    | Planned     |
| 5   | Design control SOP requires traceability verification before submission | Process | SOP-003   | Implemented |

## 6. Verification of Mitigation

| Mitigation # | Verification Method              | Evidence                             | Result  |
| ------------ | -------------------------------- | ------------------------------------ | ------- |
| 1            | Integration test — gap detection | Gap report completeness tests        | Pending |
| 2            | Unit test — coverage accuracy    | Coverage percentage validation tests | Pending |
| 3            | E2E test — visual indicators     | Gap highlighting verification        | Pending |
| 4            | E2E test — export accuracy       | CSV data correctness tests           | Pending |
| 5            | Process audit                    | SOP-003 compliance records           | Pending |

## 7. Residual Risk Acceptability

Residual risk is **acceptable** with monitoring. The combination of automated gap detection, coverage percentage visualization, and the design control SOP (SOP-003) provides multiple opportunities to identify traceability gaps before regulatory submission. The CSV export capability enables independent manual verification as a final check. The residual RPN of 8 is within the acceptable range for this situation.

## 8. Traceability

| Direction | Link Type    | Target ID     | Title                                          |
| --------- | ------------ | ------------- | ---------------------------------------------- |
| Up        | derives-from | RISK-SW-006   | Incorrect Traceability                         |
| Down      | leads-to     | HARM-001 | Regulatory Non-Compliance                      |
| Down      | mitigated-by | PRS-021        | Traceability Matrix Generation & Visualization |
| Down      | mitigated-by | SOP-003       | Design Control                                 |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
