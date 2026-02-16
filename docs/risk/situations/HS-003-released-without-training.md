---
id: HS-003
title: 'Released Software Without Required Training Completion'
status: draft
links:
  - type: derives-from
    target: RISK-SW-005
  - type: derives-from
    target: HARM-001
---

# Released Software Without Required Training Completion

## 1. Purpose

This document describes the hazardous situation in which software is released and becomes effective while personnel have not completed required training on the updated procedures. This situation arises from the software risk identified in RISK-SW-005 (Training Assignment Failure) and contributes to the harm described in HARM-001 (Regulatory Non-Compliance) and HARM-002 (Patient Safety Impact).

## 2. Risk Category

| Field       | Value                                        |
| ----------- | -------------------------------------------- |
| Category    | Hazardous Situation                          |
| Subcategory | Training compliance — release-training gap   |
| Standard    | ISO 14971, ISO 13485 §6.2, FDA 21 CFR 820.25 |

## 3. Hazard Identification

### 3.1 Hazard Description

A release containing updated SOPs, Work Instructions, or Policies is published and becomes effective. Due to training assignment failures, processing delays, or incomplete completion tracking, some or all required personnel have not completed training on the updated procedures. These personnel then perform work under outdated procedural knowledge.

### 3.2 Foreseeable Sequence of Events

1. A release containing SOP/WI/Policy changes is published
2. Training tasks are not fully assigned (RISK-SW-005) or assigned but not completed before the release becomes effective
3. Personnel perform quality-impacting work using outdated procedural knowledge
4. Work performed incorrectly due to outdated training introduces quality defects
5. Regulatory audit identifies personnel working without current training records
6. Organization faces compliance finding; if quality defects impact patient safety, additional consequences follow

### 3.3 Affected Users / Stakeholders

- Organization Members performing work under outdated procedures
- QA Managers responsible for training compliance
- Patients whose safety may be affected by inadequately trained personnel
- The organization facing regulatory findings

## 4. Risk Estimation

### Before Mitigation

| Factor         | Rating             | Justification                                                                                      |
| -------------- | ------------------ | -------------------------------------------------------------------------------------------------- |
| Severity       | 4 — Major          | Untrained personnel performing quality work can introduce defects with patient safety implications |
| Probability    | 3 — Occasional     | Gap between release effectiveness and training completion is a common real-world scenario          |
| Detectability  | 3 — Moderate       | Training gaps only visible through compliance reports; may not be checked before personnel act     |
| **Risk Level** | **High (RPN: 36)** | 4 × 3 × 3                                                                                          |

### After Mitigation

| Factor            | Rating              | Justification                                                                                       |
| ----------------- | ------------------- | --------------------------------------------------------------------------------------------------- |
| Severity          | 4 — Major           | Severity of untrained personnel performing quality work remains high                                |
| Probability       | 2 — Unlikely        | Automatic assignment, notifications, due dates, and overdue monitoring reduce the gap               |
| Detectability     | 1 — High            | Daily overdue worker, compliance reports, and training dashboards make gaps visible within 24 hours |
| **Residual Risk** | **Medium (RPN: 8)** | 4 × 2 × 1                                                                                           |

## 5. Risk Mitigation

| #   | Mitigation Measure                                                     | Type    | Target ID | Status      |
| --- | ---------------------------------------------------------------------- | ------- | --------- | ----------- |
| 1   | Automatic training task creation when Change Control becomes effective | Design  | PRS-016    | Implemented |
| 2   | In-app and email notifications to assigned members                     | Design  | PRS-016    | Implemented |
| 3   | Configurable due dates with organization-level defaults                | Design  | PRS-016    | Implemented |
| 4   | Daily overdue training notification worker (CRON)                      | Design  | PRS-016    | Implemented |
| 5   | Training compliance reporting with completion rates                    | Design  | PRS-018    | Planned     |
| 6   | Training and competency SOP defining completion requirements           | Process | SOP-008   | Implemented |
| 7   | Management review of training compliance metrics                       | Process | SOP-010   | Implemented |

## 6. Verification of Mitigation

| Mitigation # | Verification Method                | Evidence                                  | Result  |
| ------------ | ---------------------------------- | ----------------------------------------- | ------- |
| 1            | Integration test — auto-assignment | Training tasks created on release publish | Pending |
| 2            | E2E test — notifications           | Notification delivery verification        | Pending |
| 3            | Unit test — due date configuration | Due date calculation tests                | Pending |
| 4            | Integration test — overdue worker  | Overdue notification delivery tests       | Pending |
| 5            | E2E test — compliance dashboard    | Completion rate accuracy tests            | Pending |
| 6            | Process audit                      | SOP-008 compliance records                | Pending |
| 7            | Process audit                      | SOP-010 management review records         | Pending |

## 7. Residual Risk Acceptability

Residual risk is **acceptable** with monitoring. The residual RPN of 8 (medium) reflects the inherent gap between release publication and training completion. The daily overdue training worker ensures that any training gap is detected within 24 hours. The training compliance reporting feature (PRS-018) provides QA Managers with a proactive dashboard to monitor completion rates. The training SOP (SOP-008) and management review SOP (SOP-010) provide process-level governance to ensure training gaps are addressed promptly.

## 8. Traceability

| Direction | Link Type    | Target ID     | Title                         |
| --------- | ------------ | ------------- | ----------------------------- |
| Up        | derives-from | RISK-SW-005   | Training Assignment Failure   |
| Down      | leads-to     | HARM-001 | Regulatory Non-Compliance     |
| Down      | leads-to     | HARM-002 | Patient Safety Impact         |
| Down      | mitigated-by | PRS-016        | Training Task Assignment      |
| Down      | mitigated-by | PRS-018        | Training Compliance Reporting |
| Down      | mitigated-by | SOP-008       | Training and Competency       |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
