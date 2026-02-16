---
id: RISK-SEC-002
title: 'Authentication Bypass'
status: draft
links:
  - type: derives-from
    target: PRS-001
  - type: derives-from
    target: PRS-002
  - type: mitigated-by
    target: SOP-005
---

# Authentication Bypass

## 1. Purpose

This document assesses the risk of unauthorized users gaining access to QMS data by bypassing authentication mechanisms. Unauthorized access to a medical device QMS compromises data integrity, confidentiality, and regulatory compliance under FDA 21 CFR Part 11 and ISO 13485.

## 2. Risk Category

| Field       | Value                                                     |
| ----------- | --------------------------------------------------------- |
| Category    | Security                                                  |
| Subcategory | Authentication — unauthorized access                      |
| Standard    | ISO 14971, FDA 21 CFR Part 11 §11.10(d), ISO 13485 §4.2.4 |

## 3. Hazard Identification

### 3.1 Hazard Description

An attacker may gain unauthorized access to the QMS by: (a) exploiting vulnerabilities in Firebase Authentication, (b) crafting JWT tokens that bypass server-side verification, (c) accessing API endpoints without valid authentication tokens, or (d) exploiting session management weaknesses to hijack authenticated sessions.

### 3.2 Foreseeable Sequence of Events

1. An attacker identifies the QMS application and its authentication endpoints
2. The attacker exploits an authentication weakness (token forgery, session hijack, missing auth check on an API endpoint)
3. The attacker gains read or write access to QMS data
4. QMS data integrity and confidentiality are compromised
5. Tampered records undermine regulatory compliance; data breach triggers notification requirements

### 3.3 Affected Users / Stakeholders

- All organization members whose data may be exposed
- Patients whose safety data may be compromised
- The organization facing data breach liability and regulatory action
- Regulatory bodies relying on data integrity

## 4. Risk Estimation

### Before Mitigation

| Factor         | Rating             | Justification                                                                                    |
| -------------- | ------------------ | ------------------------------------------------------------------------------------------------ |
| Severity       | 5 — Catastrophic   | Unauthorized QMS access can lead to data tampering, data breach, and complete compliance failure |
| Probability    | 2 — Unlikely       | Firebase Auth is a mature platform; exploitation requires significant sophistication             |
| Detectability  | 3 — Moderate       | Unauthorized access may not be immediately apparent without active monitoring                    |
| **Risk Level** | **High (RPN: 30)** | 5 × 2 × 3                                                                                        |

### After Mitigation

| Factor            | Rating           | Justification                                                                          |
| ----------------- | ---------------- | -------------------------------------------------------------------------------------- |
| Severity          | 5 — Catastrophic | Severity of unauthorized access remains catastrophic                                   |
| Probability       | 1 — Remote       | Firebase Auth, server-side token verification, and RBAC create multiple defense layers |
| Detectability     | 1 — High         | Audit trail logs all authentication events; failed attempts are tracked and visible    |
| **Residual Risk** | **Low (RPN: 5)** | 5 × 1 × 1                                                                              |

## 5. Risk Mitigation

| #   | Mitigation Measure                                                          | Type   | Target ID | Status      |
| --- | --------------------------------------------------------------------------- | ------ | --------- | ----------- |
| 1   | Firebase Authentication with email/password and re-authentication           | Design | PRS-001    | Implemented |
| 2   | Server-side JWT token verification on all API endpoints                     | Design | PRS-001    | Implemented |
| 3   | Role-based access control (Owner, Admin, Member) with permission boundaries | Design | PRS-002    | Implemented |
| 4   | All Firestore mutations through Cloud Functions only (no client writes)     | Design | PRS-002    | Implemented |
| 5   | Audit log capture of all authentication and authorization events            | Design | PRS-009    | Implemented |
| 6   | onBeforeUserCreated trigger for user provisioning control                   | Design | PRS-001    | Implemented |

## 6. Verification of Mitigation

| Mitigation # | Verification Method                    | Evidence                                | Result  |
| ------------ | -------------------------------------- | --------------------------------------- | ------- |
| 1            | E2E test — authentication flow         | Login/logout/re-auth tests              | Pending |
| 2            | Integration test — unauthenticated API | 401 response for missing/invalid tokens | Pending |
| 3            | Integration test — RBAC enforcement    | Permission boundary tests per role      | Pending |
| 4            | Firestore security rule tests          | Client write rejection tests            | Pending |
| 5            | Integration test — auth audit logging  | Auth event audit entry tests            | Pending |
| 6            | Unit test — user creation trigger      | User provisioning control tests         | Pending |

## 7. Residual Risk Acceptability

Residual risk is **acceptable**. The system leverages Firebase Authentication (a Google-managed identity platform with enterprise-grade security), server-side token verification on every API call, and a strict backend-only-writes architecture (ADR-008) that eliminates direct client access to Firestore. The layered defense (authentication → authorization → audit logging) ensures that any bypass attempt at one layer is caught by another.

## 8. Traceability

| Direction | Link Type    | Target ID | Title                             |
| --------- | ------------ | --------- | --------------------------------- |
| Up        | derives-from | PRS-001    | Organization Lifecycle Management |
| Up        | derives-from | PRS-002    | Member Role Management            |
| Down      | mitigated-by | SOP-005   | Software Development Lifecycle    |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
