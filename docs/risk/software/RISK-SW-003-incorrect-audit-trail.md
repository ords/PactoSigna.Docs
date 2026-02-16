---
id: RISK-SW-003
title: 'Incorrect Audit Trail'
status: draft
links:
  - type: derives-from
    target: PRS-009
  - type: mitigated-by
    target: SOP-002
---

# Incorrect Audit Trail

## 1. Purpose

This document assesses the risk of audit trail entries being logged incorrectly, incompletely, or missed entirely. Audit trail integrity is mandated by FDA 21 CFR Part 11 §11.10(e) and is fundamental to the compliance posture of the QMS.

## 2. Risk Category

| Field       | Value                                                     |
| ----------- | --------------------------------------------------------- |
| Category    | Software                                                  |
| Subcategory | Data integrity — audit logging                            |
| Standard    | ISO 14971, FDA 21 CFR Part 11 §11.10(e), ISO 13485 §4.2.5 |

## 3. Hazard Identification

### 3.1 Hazard Description

Cloud Functions may fail to create audit log entries for data mutations, create entries with incorrect or incomplete data (wrong user, missing before/after state, client-provided timestamps), or entries may be tampered with after creation. Any gap or inaccuracy in the audit trail undermines the evidentiary value of the entire QMS record.

### 3.2 Foreseeable Sequence of Events

1. A Cloud Function performs a data mutation (create, update, delete) on a Firestore document
2. The audit log entry creation fails silently, is skipped due to a code path error, or captures incorrect metadata
3. An auditor queries the audit trail and finds gaps or inconsistencies
4. The organization cannot demonstrate compliance with Part 11 audit trail requirements
5. Regulatory finding issued; corrective action required

### 3.3 Affected Users / Stakeholders

- External Auditors relying on complete audit trails for compliance verification
- Regulatory Specialists managing regulatory submissions
- QA Managers responsible for QMS integrity
- The organization subject to regulatory enforcement

## 4. Risk Estimation

### Before Mitigation

| Factor         | Rating             | Justification                                                                                |
| -------------- | ------------------ | -------------------------------------------------------------------------------------------- |
| Severity       | 4 — Major          | Incomplete audit trail is a Part 11 finding; can escalate to warning letter if systemic      |
| Probability    | 3 — Occasional     | Code path errors, exception handling gaps, and race conditions can cause missed log entries  |
| Detectability  | 3 — Moderate       | Missing entries are only detectable when someone specifically looks for them or during audit |
| **Risk Level** | **High (RPN: 36)** | 4 × 3 × 3                                                                                    |

### After Mitigation

| Factor            | Rating           | Justification                                                                                    |
| ----------------- | ---------------- | ------------------------------------------------------------------------------------------------ |
| Severity          | 4 — Major        | Severity of audit trail gaps remains inherently high for compliance                              |
| Probability       | 1 — Remote       | Centralized audit logging with shared enum and server timestamps minimizes missed entries        |
| Detectability     | 1 — High         | Failed operations also logged; Firestore rules prevent client writes; completeness is verifiable |
| **Residual Risk** | **Low (RPN: 4)** | 4 × 1 × 1                                                                                        |

## 5. Risk Mitigation

| #   | Mitigation Measure                                                           | Type    | Target ID | Status      |
| --- | ---------------------------------------------------------------------------- | ------- | --------- | ----------- |
| 1   | Every Cloud Function mutation creates an immutable audit log entry           | Design  | PRS-009    | Implemented |
| 2   | Shared TypeScript enum for all audit action types (compile-time enforcement) | Design  | PRS-009    | Implemented |
| 3   | Server-generated timestamps only (client timestamps rejected)                | Design  | PRS-009    | Implemented |
| 4   | Firestore security rules deny all client writes to audit log collections     | Design  | PRS-009    | Implemented |
| 5   | Failed operations logged with error details                                  | Design  | PRS-009    | Implemented |
| 6   | Audit log entries include before/after state for change tracking             | Design  | PRS-009    | Implemented |
| 7   | Document control SOP includes audit trail verification procedures            | Process | SOP-002   | Implemented |

## 6. Verification of Mitigation

| Mitigation # | Verification Method              | Evidence                               | Result  |
| ------------ | -------------------------------- | -------------------------------------- | ------- |
| 1            | Integration tests per mutation   | Audit entry creation for each endpoint | Pending |
| 2            | TypeScript compilation           | Enum completeness check                | Pending |
| 3            | Unit test — timestamp validation | Server timestamp enforcement tests     | Pending |
| 4            | Security rule tests              | Client write rejection tests           | Pending |
| 5            | Unit test — error logging        | Failed operation audit entry tests     | Pending |
| 6            | Unit test — state capture        | Before/after state comparison tests    | Pending |
| 7            | Process audit                    | SOP-002 compliance records             | Pending |

## 7. Residual Risk Acceptability

Residual risk is **acceptable**. The centralized audit logging architecture ensures every mutation path creates an audit entry. Compile-time enforcement via the shared TypeScript enum prevents new mutation types from being added without corresponding audit actions. Server-generated timestamps and Firestore security rules eliminate client-side tampering vectors. The combination ensures audit trail completeness and integrity are maintained by design.

## 8. Traceability

| Direction | Link Type    | Target ID | Title             |
| --------- | ------------ | --------- | ----------------- |
| Up        | derives-from | PRS-009    | Audit Log Capture |
| Down      | mitigated-by | SOP-002   | Document Control  |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
