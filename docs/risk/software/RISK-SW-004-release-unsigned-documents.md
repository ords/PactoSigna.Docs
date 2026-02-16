---
id: RISK-SW-004
title: 'Release with Unsigned Documents'
status: draft
links:
  - type: derives-from
    target: PRS-012
  - type: mitigated-by
    target: SOP-006
---

# Release with Unsigned Documents

## 1. Purpose

This document assesses the risk of the release workflow allowing publication of a release before all required departmental signatures have been collected. This directly violates ISO 13485 §7.3.7 design change control requirements and FDA 21 CFR Part 11 electronic signature mandates.

## 2. Risk Category

| Field       | Value                                               |
| ----------- | --------------------------------------------------- |
| Category    | Software                                            |
| Subcategory | Workflow integrity — release signature enforcement  |
| Standard    | ISO 14971, ISO 13485 §7.3.7, FDA 21 CFR Part 820.30 |

## 3. Hazard Identification

### 3.1 Hazard Description

The release workflow may contain a defect that allows a release to transition from Open (In Review) to Closed (Published) without all required departmental signatures being present. This could occur through API bypass, race conditions in signature collection, or incorrect signature completeness validation logic.

### 3.2 Foreseeable Sequence of Events

1. An Admin creates a release and submits it for review
2. Required reviewers are notified but not all departments complete their review
3. A defect in the publish validation logic allows the release to be published prematurely
4. Documents become effective without full departmental approval
5. Regulatory audit discovers published release lacking required signatures
6. Organization faces regulatory non-compliance finding

### 3.3 Affected Users / Stakeholders

- QA Managers responsible for release completeness
- Departmental reviewers whose sign-off was bypassed
- Regulatory Specialists whose submissions reference the release
- External Auditors verifying signature completeness

## 4. Risk Estimation

### Before Mitigation

| Factor         | Rating             | Justification                                                                               |
| -------------- | ------------------ | ------------------------------------------------------------------------------------------- |
| Severity       | 5 — Catastrophic   | Published release without required signatures is a fundamental design control failure       |
| Probability    | 2 — Unlikely       | Requires specific defect in server-side validation logic; not easily triggered accidentally |
| Detectability  | 2 — Good           | Missing signatures are visible in the release detail view and audit trail                   |
| **Risk Level** | **High (RPN: 20)** | 5 × 2 × 2                                                                                   |

### After Mitigation

| Factor            | Rating           | Justification                                                                                     |
| ----------------- | ---------------- | ------------------------------------------------------------------------------------------------- |
| Severity          | 5 — Catastrophic | Severity remains catastrophic regardless of controls                                              |
| Probability       | 1 — Remote       | Server-side validation with explicit signature completeness check before status transition        |
| Detectability     | 1 — High         | Every lifecycle event creates audit entry; signature gaps are immediately visible in release view |
| **Residual Risk** | **Low (RPN: 5)** | 5 × 1 × 1                                                                                         |

## 5. Risk Mitigation

| #   | Mitigation Measure                                                              | Type    | Target ID | Status      |
| --- | ------------------------------------------------------------------------------- | ------- | --------- | ----------- |
| 1   | Server-side validation prevents publish until all required signatures collected | Design  | PRS-012    | Implemented |
| 2   | Published releases are immutable (no post-publication modification)             | Design  | PRS-012    | Implemented |
| 3   | Every release lifecycle event creates immutable audit log entry                 | Design  | PRS-012    | Implemented |
| 4   | Notification to all required reviewers on release submission                    | Design  | PRS-012    | Implemented |
| 5   | Change control SOP defines release approval workflow and verification steps     | Process | SOP-006   | Implemented |

## 6. Verification of Mitigation

| Mitigation # | Verification Method                  | Evidence                            | Result  |
| ------------ | ------------------------------------ | ----------------------------------- | ------- |
| 1            | E2E test — premature publish attempt | Publish rejection with missing sigs | Pending |
| 2            | Security rule + API tests            | Post-publish modification denial    | Pending |
| 3            | Integration test — lifecycle events  | Audit entries for all transitions   | Pending |
| 4            | E2E test — notification delivery     | Reviewer notification on submission | Pending |
| 5            | Process audit                        | SOP-006 compliance records          | Pending |

## 7. Residual Risk Acceptability

Residual risk is **acceptable**. The server-side signature completeness check is a hard gate — no code path exists to publish a release without satisfying the validation. The immutability constraint ensures that if a release passes validation and is published, the state cannot be retroactively corrupted. Combined with audit logging and the change control SOP, the system provides robust protection against unsigned releases.

## 8. Traceability

| Direction | Link Type    | Target ID | Title            |
| --------- | ------------ | --------- | ---------------- |
| Up        | derives-from | PRS-012    | Release Workflow |
| Down      | mitigated-by | SOP-006   | Change Control   |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
