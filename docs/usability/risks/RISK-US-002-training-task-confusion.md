---
id: RISK-US-002
title: 'Training Task Confusion'
status: draft
links:
  - type: derives-from
    target: UN-007
  - type: mitigated-by
    target: SOP-008
---

# Training Task Confusion

## 1. Purpose

This document assesses the usability risk that users confuse training task assignments with document reviews, leading to missed compliance deadlines. This is a use-related risk per IEC 62366-1 impacting training compliance for the QMS SaMD.

## 2. Risk Category

| Field       | Value                                      |
| ----------- | ------------------------------------------ |
| Category    | Usability                                  |
| Subcategory | Task differentiation — training vs. review |
| Standard    | IEC 62366-1, ISO 14971, ISO 13485 §6.2     |

## 3. Hazard Identification

### 3.1 Hazard Description

Training tasks and document review tasks appear in the same notification stream and task lists. Users may mistake a training assignment (requiring quiz completion and competency demonstration) for a simple document review (requiring only acknowledgement), resulting in incomplete training records and compliance gaps.

### 3.2 Foreseeable Sequence of Events

1. User receives notification of a new task assignment after a release is published
2. User assumes the task is a document review and only skims the content
3. User does not complete the required quiz or competency assessment
4. Training record remains incomplete; overdue notification fires but is ignored
5. Regulatory audit reveals gaps in personnel competency records per ISO 13485 §6.2

### 3.3 Affected Users

- Developer Users receiving training assignments
- QA Managers monitoring training completion dashboards
- Regulatory Specialists verifying competency records during audits

## 4. Risk Estimation

### Before Mitigation

| Factor         | Rating               | Justification                                                                  |
| -------------- | -------------------- | ------------------------------------------------------------------------------ |
| Severity       | 3 — Moderate         | Incomplete training records are a common audit finding; correctable but costly |
| Probability    | 3 — Occasional       | Similar-looking task types are a known source of user confusion                |
| Detectability  | 2 — Good             | Overdue training CRON notifications eventually surface missed tasks            |
| **Risk Level** | **Medium (RPN: 18)** | 3 × 3 × 2                                                                      |

### After Mitigation

| Factor            | Rating           | Justification                                                             |
| ----------------- | ---------------- | ------------------------------------------------------------------------- |
| Severity          | 3 — Moderate     | Inherent severity of compliance gap unchanged                             |
| Probability       | 1 — Remote       | Distinct visual treatment and SOP guidance reduce confusion               |
| Detectability     | 1 — High         | Dashboard and overdue worker make incomplete training immediately visible |
| **Residual Risk** | **Low (RPN: 3)** | 3 × 1 × 1                                                                 |

## 5. Risk Mitigation

| #   | Mitigation Measure                                                   | Type    | Target ID | Status      |
| --- | -------------------------------------------------------------------- | ------- | --------- | ----------- |
| 1   | Distinct visual badges differentiating training tasks from reviews   | Design  | SOP-008   | Planned     |
| 2   | Training SOP with clear instructions on task completion requirements | Process | SOP-008   | Implemented |
| 3   | Overdue training worker sends escalation notifications               | Design  | SOP-008   | Implemented |

## 6. Verification of Mitigation

| Mitigation # | Verification Method             | Evidence                      | Result  |
| ------------ | ------------------------------- | ----------------------------- | ------- |
| 1            | Usability evaluation            | Task differentiation accuracy | Pending |
| 2            | Process audit                   | SOP-008 compliance records    | Pending |
| 3            | E2E test — overdue notification | Training overdue worker tests | Pending |

## 7. Residual Risk Acceptability

Residual risk is **acceptable**. The combination of distinct visual treatment, clear SOP guidance, and automated overdue notifications ensures training tasks are correctly identified and completed within required timelines.

## 8. Traceability

| Direction | Link Type    | Target ID | Title                   |
| --------- | ------------ | --------- | ----------------------- |
| Up        | derives-from | UN-007    | Training & Competency   |
| Down      | mitigated-by | SOP-008   | Training Compliance SOP |

## 9. Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-16 | —      | Initial draft |
