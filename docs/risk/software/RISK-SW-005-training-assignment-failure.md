---
id: RISK-SW-005
title: 'Training Assignment Failure'
status: draft
links:
  - type: derives-from
    target: PRS-016
  - type: mitigated-by
    target: SOP-008
---

# Training Assignment Failure

## 1. Purpose

This document assesses the risk of the training assignment system failing to create training tasks for required personnel when documents are updated through the release process. Failure to ensure personnel competency is a violation of ISO 13485 §6.2 and FDA 21 CFR 820.25.

## 2. Risk Category

| Field       | Value                                        |
| ----------- | -------------------------------------------- |
| Category    | Software                                     |
| Subcategory | Workflow integrity — training automation     |
| Standard    | ISO 14971, ISO 13485 §6.2, FDA 21 CFR 820.25 |

## 3. Hazard Identification

### 3.1 Hazard Description

The training assignment worker may fail to create training tasks for all required members when a Change Control containing SOP, Work Instruction, or Policy changes becomes effective. This could occur due to Cloud Tasks queue failures, incorrect department filtering, worker crashes, or duplicate prevention logic incorrectly suppressing legitimate assignments.

### 3.2 Foreseeable Sequence of Events

1. A release containing SOP changes is published
2. The training assignment worker is triggered via Cloud Tasks
3. The worker fails to process all required department/member combinations
4. Some members do not receive training tasks for updated procedures
5. Members operate under outdated procedure knowledge
6. Regulatory audit discovers personnel working without required training records

### 3.3 Affected Users / Stakeholders

- Organization Members who need training on updated procedures
- QA Managers responsible for training compliance metrics
- Regulatory Specialists demonstrating training compliance during audits
- Patients whose safety depends on properly trained personnel

## 4. Risk Estimation

### Before Mitigation

| Factor         | Rating             | Justification                                                                                        |
| -------------- | ------------------ | ---------------------------------------------------------------------------------------------------- |
| Severity       | 4 — Major          | Missing training records is a significant regulatory finding; could impact patient safety indirectly |
| Probability    | 3 — Occasional     | Cloud Tasks failures, worker crashes, and edge cases in department filtering are realistic           |
| Detectability  | 3 — Moderate       | Missing assignments only detectable via training compliance reports or when preparing for audit      |
| **Risk Level** | **High (RPN: 36)** | 4 × 3 × 3                                                                                            |

### After Mitigation

| Factor            | Rating              | Justification                                                                                  |
| ----------------- | ------------------- | ---------------------------------------------------------------------------------------------- |
| Severity          | 4 — Major           | Severity of missing training records remains high for compliance                               |
| Probability       | 2 — Unlikely        | Duplicate prevention, configurable due dates, and manual assignment fallback reduce likelihood |
| Detectability     | 1 — High            | Overdue training worker (daily CRON) and compliance reporting make gaps visible quickly        |
| **Residual Risk** | **Medium (RPN: 8)** | 4 × 2 × 1                                                                                      |

## 5. Risk Mitigation

| #   | Mitigation Measure                                                   | Type    | Target ID | Status      |
| --- | -------------------------------------------------------------------- | ------- | --------- | ----------- |
| 1   | Automatic training task creation on Change Control effectiveness     | Design  | PRS-016    | Implemented |
| 2   | Duplicate task prevention (no redundant assignments)                 | Design  | PRS-016    | Implemented |
| 3   | Configurable due dates with organization-level defaults              | Design  | PRS-016    | Implemented |
| 4   | Manual assignment capability as fallback for missed auto-assignments | Design  | PRS-016    | Implemented |
| 5   | Daily overdue training notification worker (CRON)                    | Design  | PRS-016    | Implemented |
| 6   | In-app and email notification on task assignment                     | Design  | PRS-016    | Implemented |
| 7   | Training and competency SOP defining verification procedures         | Process | SOP-008   | Implemented |

## 6. Verification of Mitigation

| Mitigation # | Verification Method                | Evidence                              | Result  |
| ------------ | ---------------------------------- | ------------------------------------- | ------- |
| 1            | Integration test — release publish | Training tasks created per department | Pending |
| 2            | Unit test — duplicate prevention   | Duplicate assignment rejection tests  | Pending |
| 3            | Unit test — due date calculation   | Configurable due date tests           | Pending |
| 4            | E2E test — manual assignment       | Ad hoc assignment creation tests      | Pending |
| 5            | Integration test — CRON worker     | Overdue notification delivery tests   | Pending |
| 6            | E2E test — notifications           | Email and in-app notification tests   | Pending |
| 7            | Process audit                      | SOP-008 compliance records            | Pending |

## 7. Residual Risk Acceptability

Residual risk is **acceptable** with monitoring. While the residual RPN is 8 (medium), the daily overdue training worker provides continuous monitoring that detects any gaps within 24 hours. Manual assignment capability provides an immediate remediation path. The training compliance reporting feature (PRS-018) enables QA Managers to proactively identify and address training gaps before they become audit findings.

## 8. Traceability

| Direction | Link Type    | Target ID | Title                    |
| --------- | ------------ | --------- | ------------------------ |
| Up        | derives-from | PRS-016    | Training Task Assignment |
| Down      | mitigated-by | SOP-008   | Training and Competency  |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
