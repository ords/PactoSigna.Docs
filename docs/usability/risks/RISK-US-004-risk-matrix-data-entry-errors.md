---
id: RISK-US-004
title: 'Risk Matrix Data Entry Errors'
status: draft
links:
  - type: derives-from
    target: UN-008
  - type: mitigated-by
    target: SRS-701
---

# Risk Matrix Data Entry Errors

## 1. Purpose

This document assesses the usability risk that users enter incorrect severity/probability ratings due to unclear UI labeling, leading to underestimated risks in the QMS matrix. This is a use-related risk per IEC 62366-1 with direct impact on ISO 14971 risk management.

## 2. Risk Category

| Field       | Value                                     |
| ----------- | ----------------------------------------- |
| Category    | Usability                                 |
| Subcategory | Data entry accuracy — risk matrix ratings |
| Standard    | IEC 62366-1, ISO 14971 §5.4               |

## 3. Hazard Identification

### 3.1 Hazard Description

The risk matrix UI requires users to select severity and probability ratings from dropdown or matrix cells. Ambiguous labels, lack of contextual definitions, or inverted scale expectations (e.g., 1 = highest vs. 1 = lowest) may cause users to assign incorrect ratings, resulting in underestimated risk levels that bypass required mitigations.

### 3.2 Foreseeable Sequence of Events

1. User creates or updates a risk entry in the risk management module
2. User selects severity and probability values without fully understanding the scale definitions
3. Incorrect ratings produce a lower risk level than warranted
4. Risk is classified as acceptable when it should require mitigation
5. Unmitigated hazard persists in the product risk management file

### 3.3 Affected Users

- QA Managers entering and reviewing risk assessments
- Regulatory Specialists validating risk acceptability
- Auditors reviewing the risk management file for completeness

## 4. Risk Estimation

### Before Mitigation

| Factor         | Rating             | Justification                                                                |
| -------------- | ------------------ | ---------------------------------------------------------------------------- |
| Severity       | 4 — Major          | Underestimated risks may lead to unmitigated patient safety hazards          |
| Probability    | 2 — Unlikely       | Scale confusion is common but most users receive prior training on ISO 14971 |
| Detectability  | 3 — Moderate       | Incorrect ratings are not self-evidently wrong without cross-review          |
| **Risk Level** | **High (RPN: 24)** | 4 × 2 × 3                                                                    |

### After Mitigation

| Factor            | Rating           | Justification                                                          |
| ----------------- | ---------------- | ---------------------------------------------------------------------- |
| Severity          | 4 — Major        | Inherent severity of underestimated risk unchanged                     |
| Probability       | 1 — Remote       | Contextual tooltips and validation rules reduce entry errors           |
| Detectability     | 1 — High         | Color-coded matrix and summary review make ratings visually verifiable |
| **Residual Risk** | **Low (RPN: 4)** | 4 × 1 × 1                                                              |

## 5. Risk Mitigation

| #   | Mitigation Measure                                                     | Type   | Target ID | Status  |
| --- | ---------------------------------------------------------------------- | ------ | --------- | ------- |
| 1   | Contextual tooltips with ISO 14971 scale definitions on each rating    | Design | SRS-701   | Planned |
| 2   | Color-coded risk matrix reflecting calculated risk levels in real time | Design | SRS-701   | Planned |
| 3   | Confirmation dialog summarizing ratings before submission              | Design | SRS-701   | Planned |

## 6. Verification of Mitigation

| Mitigation # | Verification Method            | Evidence                      | Result  |
| ------------ | ------------------------------ | ----------------------------- | ------- |
| 1            | Usability evaluation           | Rating accuracy ≥ 95%         | Pending |
| 2            | Unit test — color mapping      | Matrix color assignment tests | Pending |
| 3            | E2E test — confirmation dialog | Submission guard tests        | Pending |

## 7. Residual Risk Acceptability

Residual risk is **acceptable**. Contextual definitions, real-time visual feedback, and confirmation dialogs collectively ensure that risk ratings accurately reflect the assessor's intent before they are recorded in the risk management file.

## 8. Traceability

| Direction | Link Type    | Target ID | Title           |
| --------- | ------------ | --------- | --------------- |
| Up        | derives-from | UN-008    | Risk Management |
| Down      | mitigated-by | SRS-701   | Risk Matrix UI  |

## 9. Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-16 | —      | Initial draft |
