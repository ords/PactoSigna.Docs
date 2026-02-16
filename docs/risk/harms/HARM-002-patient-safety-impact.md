---
id: HARM-002
title: 'Patient Safety Impact from Non-Compliant Device Software'
status: draft
links:
  - type: derives-from
    target: HS-003
  - type: derives-from
    target: HS-001
---

# Patient Safety Impact from Non-Compliant Device Software

## 1. Purpose

This document describes the ultimate harm of patient safety impact arising when medical device software is developed, verified, or released using a QMS that has failed to ensure compliance. While PactoSigna is a QMS tool (not a medical device itself), its failures can indirectly contribute to patient harm by allowing non-compliant device software to reach patients.

## 2. Risk Category

| Field       | Value                                            |
| ----------- | ------------------------------------------------ |
| Category    | Harm                                             |
| Subcategory | Patient safety — indirect impact via QMS failure |
| Standard    | ISO 14971, IEC 62304, ISO 13485 §7.1             |

## 3. Hazard Identification

### 3.1 Hazard Description

The QMS managed by PactoSigna fails to ensure proper design controls, traceability, or training for medical device software development. As a result, device software with undetected defects, incomplete verification, or inadequate risk controls reaches patients. The harm pathway is indirect: PactoSigna failure → QMS inadequacy → device software defects → patient impact.

### 3.2 Foreseeable Sequence of Events

1. PactoSigna fails to detect traceability gaps (HS-001) or training gaps (HS-003) during the device software development lifecycle
2. Medical device software is released with incomplete verification or by inadequately trained personnel
3. The device software contains defects that were not caught by the compromised QMS process
4. The defective device software is deployed in a clinical or patient-facing context
5. A patient experiences an adverse event related to the software defect
6. Investigation traces the root cause to QMS process failures

### 3.3 Affected Users / Stakeholders

- Patients who are harmed by defective device software
- Healthcare providers using the defective device
- The device manufacturer facing product liability and recall obligations
- PactoSigna as a contributing factor in the root cause analysis
- Regulatory bodies requiring corrective action

## 4. Risk Estimation

### Before Mitigation

| Factor         | Rating           | Justification                                                                                       |
| -------------- | ---------------- | --------------------------------------------------------------------------------------------------- |
| Severity       | 5 — Catastrophic | Patient safety impact from medical device software defects can range from injury to death           |
| Probability    | 1 — Remote       | Requires multiple cascading failures: QMS tool failure + manufacturing QMS reliance + device defect |
| **Risk Level** | **Medium**       | Catastrophic severity but very low probability due to multiple independent failure points required  |

### After Mitigation

| Factor            | Rating           | Justification                                                                                       |
| ----------------- | ---------------- | --------------------------------------------------------------------------------------------------- |
| Severity          | 5 — Catastrophic | Patient safety severity is inherently catastrophic                                                  |
| Probability       | 1 — Remote       | Upstream mitigations + the device manufacturer's own QMS controls provide additional defense layers |
| **Residual Risk** | **Low**          | Probability remains remote; upstream mitigations reduce contributing risk factors                   |

## 5. Risk Mitigation

This harm requires cascading failures across multiple independent systems. PactoSigna's mitigations address its contribution to the chain:

| #   | Mitigation Measure                                                  | Type    | Target ID | Status      |
| --- | ------------------------------------------------------------------- | ------- | --------- | ----------- |
| 1   | Complete traceability from User Needs → Requirements → Tests        | Design  | PRS-021    | Planned     |
| 2   | Automated training assignment with completion tracking              | Design  | PRS-016    | Implemented |
| 3   | Release workflow preventing publication without required signatures | Design  | PRS-012    | Implemented |
| 4   | Risk-requirement traceability ensuring all risks are addressed      | Design  | PRS-020    | Planned     |
| 5   | Risk management SOP defining risk analysis procedures               | Process | SOP-004   | Implemented |
| 6   | Verification and validation SOP defining testing requirements       | Process | SOP-007   | Implemented |

**Note:** The device manufacturer's own QMS processes, independent quality checks, and regulatory review provide additional mitigation layers beyond PactoSigna's controls.

## 6. Verification of Mitigation

Verification is performed at the upstream risk and device manufacturer levels:

| Upstream Risk | Verification Reference                        |
| ------------- | --------------------------------------------- |
| HS-001  | Traceability completeness verification        |
| HS-003  | Training completion verification              |
| Device QMS    | Outside PactoSigna scope (manufacturer's V&V) |

## 7. Residual Risk Acceptability

Residual risk is **acceptable**. The harm pathway requires multiple independent failures: (1) PactoSigna's compliance controls fail, (2) the device manufacturer does not catch the gap through their own quality processes, (3) the resulting device defect is safety-critical, and (4) the defect reaches patients. PactoSigna's mitigations address factor (1) comprehensively. The overall benefit-risk analysis strongly favors PactoSigna's use: automated compliance checking, traceability, and training management are significantly more reliable than manual QMS processes, reducing the net patient safety risk.

## 8. Traceability

| Direction | Link Type    | Target ID    | Title                                              |
| --------- | ------------ | ------------ | -------------------------------------------------- |
| Up        | derives-from | HS-001 | Regulatory Submission with Incomplete Traceability |
| Up        | derives-from | HS-003 | Released Software Without Required Training        |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
