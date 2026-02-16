---
id: RISK-SW-002
title: 'Electronic Signature Bypass'
status: draft
links:
  - type: derives-from
    target: PRS-014
  - type: mitigated-by
    target: SOP-006
---

# Electronic Signature Bypass

## 1. Purpose

This document assesses the risk of electronic signature integrity being compromised, including bypassing authentication, forging signatures, or transferring signatures between records. This is a critical compliance risk under FDA 21 CFR Part 11 and ISO 14971.

## 2. Risk Category

| Field       | Value                                                  |
| ----------- | ------------------------------------------------------ |
| Category    | Software                                               |
| Subcategory | Signature integrity — electronic records compliance    |
| Standard    | ISO 14971, FDA 21 CFR Part 11 §11.50, §11.100, §11.200 |

## 3. Hazard Identification

### 3.1 Hazard Description

The electronic signature mechanism may be circumvented such that: (a) a signature is created without proper re-authentication, (b) a signature record is modified or deleted after creation, (c) a signature is transferred from one record to another, or (d) ordering constraints are bypassed allowing premature final approval.

### 3.2 Foreseeable Sequence of Events

1. A user or malicious actor exploits a flaw in the signing ceremony
2. A signature is captured without proper identity verification, or an existing signature is modified/reattached to a different resource
3. A release is published with invalid or fraudulent signatures
4. Regulatory submission includes legally invalid electronic signatures
5. FDA audit discovers Part 11 non-compliance

### 3.3 Affected Users / Stakeholders

- Regulatory Specialists whose submissions depend on valid signatures
- QA Managers responsible for release integrity
- Signers whose identity may be impersonated
- The organization facing regulatory action

## 4. Risk Estimation

### Before Mitigation

| Factor         | Rating             | Justification                                                                              |
| -------------- | ------------------ | ------------------------------------------------------------------------------------------ |
| Severity       | 5 — Catastrophic   | Part 11 non-compliance can result in FDA warning letter, consent decree, or product recall |
| Probability    | 2 — Unlikely       | Requires deliberate exploitation; accidental bypass is low given server-side enforcement   |
| Detectability  | 2 — Good           | Audit trail captures signature events; anomalies visible in signature records              |
| **Risk Level** | **High (RPN: 20)** | 5 × 2 × 2                                                                                  |

### After Mitigation

| Factor            | Rating           | Justification                                                                                |
| ----------------- | ---------------- | -------------------------------------------------------------------------------------------- |
| Severity          | 5 — Catastrophic | Severity of Part 11 non-compliance remains inherently catastrophic                           |
| Probability       | 1 — Remote       | Server-side enforcement, immutability, and re-authentication make bypass extremely difficult |
| Detectability     | 1 — High         | Every signature attempt (success and failure) is logged in immutable audit trail             |
| **Residual Risk** | **Low (RPN: 5)** | 5 × 1 × 1                                                                                    |

## 5. Risk Mitigation

| #   | Mitigation Measure                                                             | Type    | Target ID | Status      |
| --- | ------------------------------------------------------------------------------ | ------- | --------- | ----------- |
| 1   | Re-authentication required before every signature                              | Design  | PRS-014    | Implemented |
| 2   | Signature records are immutable (no edit, modify, or delete)                   | Design  | PRS-014    | Implemented |
| 3   | Signatures bound to specific content version via commit SHAs                   | Design  | PRS-014    | Implemented |
| 4   | Ordering constraints enforced (Final Approver only after all reviewers)        | Design  | PRS-014    | Implemented |
| 5   | Duplicate signature prevention per user per resource                           | Design  | PRS-014    | Implemented |
| 6   | Failed authentication attempts logged and lockout after 3 failures             | Design  | PRS-014    | Implemented |
| 7   | Change control SOP requiring signature verification before release publication | Process | SOP-006   | Implemented |

## 6. Verification of Mitigation

| Mitigation # | Verification Method              | Evidence                            | Result  |
| ------------ | -------------------------------- | ----------------------------------- | ------- |
| 1            | E2E test — signing ceremony      | Re-auth prompt verification         | Pending |
| 2            | Security rule tests              | Firestore rules deny mutation       | Pending |
| 3            | Unit test — commit SHA binding   | Signature-content linkage tests     | Pending |
| 4            | E2E test — ordering enforcement  | Premature approval rejection tests  | Pending |
| 5            | Unit test — duplicate prevention | Duplicate signature rejection tests | Pending |
| 6            | E2E test — lockout               | Failed auth lockout tests           | Pending |
| 7            | Process audit                    | SOP-006 compliance records          | Pending |

## 7. Residual Risk Acceptability

Residual risk is **acceptable**. The system enforces signature integrity through multiple independent server-side controls: re-authentication, immutability, content binding, ordering constraints, and comprehensive audit logging. No client-side bypass path exists because all signature operations are processed exclusively through Cloud Functions (ADR-008). The combination of technical controls and process controls (SOP-006) provides defense in depth.

## 8. Traceability

| Direction | Link Type    | Target ID | Title                        |
| --------- | ------------ | --------- | ---------------------------- |
| Up        | derives-from | PRS-014    | Electronic Signature Capture |
| Down      | mitigated-by | SOP-006   | Change Control               |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
