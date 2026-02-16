---
id: RISK-US-003
title: 'Document Sync Status Misinterpretation'
status: draft
links:
  - type: derives-from
    target: UN-003
  - type: mitigated-by
    target: SDD-005
---

# Document Sync Status Misinterpretation

## 1. Purpose

This document assesses the usability risk that users misinterpret sync status indicators (pending/in-progress/failed), leading to reliance on stale documents. This is a use-related risk per IEC 62366-1 for the QMS SaMD.

## 2. Risk Category

| Field       | Value                                           |
| ----------- | ----------------------------------------------- |
| Category    | Usability                                       |
| Subcategory | Status comprehension — document synchronization |
| Standard    | IEC 62366-1, ISO 14971                          |

## 3. Hazard Identification

### 3.1 Hazard Description

The document sync process exposes status indicators (pending, in_progress, completed, failed) through the UI. Users may misinterpret a "pending" or "failed" status as "completed," or may not notice the status at all, leading them to make release or compliance decisions based on outdated document versions.

### 3.2 Foreseeable Sequence of Events

1. User triggers a repository sync or sync is triggered by a GitHub webhook
2. Sync enters "in_progress" or fails silently with a "failed" status
3. User navigates to documents and sees content without checking sync status
4. User includes stale document versions in a release or audit package
5. Released QMS package contains outdated procedures or specifications

### 3.3 Affected Users

- QA Managers verifying document currency before releases
- Developer Users authoring and reviewing documents
- Regulatory Specialists compiling audit documentation

## 4. Risk Estimation

### Before Mitigation

| Factor         | Rating             | Justification                                                                     |
| -------------- | ------------------ | --------------------------------------------------------------------------------- |
| Severity       | 3 — Moderate       | Stale documents in releases require correction but are typically caught in review |
| Probability    | 3 — Occasional     | Status indicators are easily overlooked in complex dashboards                     |
| Detectability  | 3 — Moderate       | Stale content may not be obviously wrong without comparing to source              |
| **Risk Level** | **High (RPN: 27)** | 3 × 3 × 3                                                                         |

### After Mitigation

| Factor            | Rating           | Justification                                                    |
| ----------------- | ---------------- | ---------------------------------------------------------------- |
| Severity          | 3 — Moderate     | Inherent severity unchanged                                      |
| Probability       | 1 — Remote       | Provider chain blocks dependent UI until sync resolves           |
| Detectability     | 1 — High         | Prominent banners and loading states make sync status unmissable |
| **Residual Risk** | **Low (RPN: 3)** | 3 × 1 × 1                                                        |

## 5. Risk Mitigation

| #   | Mitigation Measure                                             | Type   | Target ID | Status      |
| --- | -------------------------------------------------------------- | ------ | --------- | ----------- |
| 1   | App shell provider chain blocks UI until sync state resolves   | Design | SDD-005    | Implemented |
| 2   | Prominent sync status banner on document list and detail pages | Design | SDD-005    | Implemented |
| 3   | Failed sync displays actionable error with retry option        | Design | SDD-005    | Implemented |

## 6. Verification of Mitigation

| Mitigation # | Verification Method         | Evidence                          | Result  |
| ------------ | --------------------------- | --------------------------------- | ------- |
| 1            | Unit test — provider chain  | Loading state propagation tests   | Pending |
| 2            | Usability evaluation        | Status recognition accuracy ≥ 95% | Pending |
| 3            | E2E test — failed sync flow | Retry action completion tests     | Pending |

## 7. Residual Risk Acceptability

Residual risk is **acceptable**. The provider chain ensures no stale content is displayed while a sync is in progress, and prominent banners make failure states immediately visible with clear recovery actions.

## 8. Traceability

| Direction | Link Type    | Target ID | Title                      |
| --------- | ------------ | --------- | -------------------------- |
| Up        | derives-from | UN-003    | Document Management & Sync |
| Down      | mitigated-by | SDD-005    | App Shell Provider Chain   |

## 9. Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-16 | —      | Initial draft |
