---
id: HS-002
title: 'Audit with Tampered or Missing Audit Logs'
status: draft
links:
  - type: derives-from
    target: RISK-SW-003
  - type: derives-from
    target: HARM-001
---

# Audit with Tampered or Missing Audit Logs

## 1. Purpose

This document describes the hazardous situation in which an organization undergoes a regulatory audit and cannot produce complete, trustworthy audit logs. This situation arises from the software risk identified in RISK-SW-003 (Incorrect Audit Trail) and contributes to the harm described in HARM-001 (Regulatory Non-Compliance).

## 2. Risk Category

| Field       | Value                                                     |
| ----------- | --------------------------------------------------------- |
| Category    | Hazardous Situation                                       |
| Subcategory | Audit readiness — audit trail integrity                   |
| Standard    | ISO 14971, FDA 21 CFR Part 11 §11.10(e), ISO 13485 §4.2.5 |

## 3. Hazard Identification

### 3.1 Hazard Description

During a regulatory audit (FDA inspection, Notified Body audit, internal audit), the auditor requests audit trail records for specific QMS activities. The organization discovers that audit log entries are missing, incomplete, or show evidence of tampering (inconsistent timestamps, missing before/after states, gaps in the event sequence).

### 3.2 Foreseeable Sequence of Events

1. Regulatory auditor requests audit trail for specific time period or specific QMS activities
2. Organization queries PactoSigna audit logs
3. Auditor identifies gaps (missing entries for known mutations), inconsistencies (timestamps out of sequence), or incomplete records (missing before/after state)
4. Auditor issues a finding for Part 11 §11.10(e) non-compliance
5. Organization must implement corrective actions and may face enforcement action if findings are systemic

### 3.3 Affected Users / Stakeholders

- External Auditors who cannot perform their assessment
- QA Managers responsible for audit readiness
- Regulatory Specialists managing audit responses
- The organization facing regulatory findings and corrective action burden

## 4. Risk Estimation

### Before Mitigation

| Factor         | Rating             | Justification                                                                             |
| -------------- | ------------------ | ----------------------------------------------------------------------------------------- |
| Severity       | 4 — Major          | Audit trail deficiency is a direct Part 11 finding; can escalate to warning letter        |
| Probability    | 2 — Unlikely       | Cloud Functions architecture ensures centralized logging; gaps require code-level defects |
| Detectability  | 3 — Moderate       | Gaps only discovered when auditor specifically queries for and cross-references events    |
| **Risk Level** | **High (RPN: 24)** | 4 × 2 × 3                                                                                 |

### After Mitigation

| Factor            | Rating           | Justification                                                                                           |
| ----------------- | ---------------- | ------------------------------------------------------------------------------------------------------- |
| Severity          | 4 — Major        | Severity of audit trail findings remains high                                                           |
| Probability       | 1 — Remote       | Immutable entries, server timestamps, and security rules make tampering and omission extremely unlikely |
| Detectability     | 1 — High         | Audit log viewing with filtering (PRS-010) enables proactive completeness verification                   |
| **Residual Risk** | **Low (RPN: 4)** | 4 × 1 × 1                                                                                               |

## 5. Risk Mitigation

| #   | Mitigation Measure                                                   | Type    | Target ID | Status      |
| --- | -------------------------------------------------------------------- | ------- | --------- | ----------- |
| 1   | Immutable audit log entries with server-generated timestamps         | Design  | PRS-009    | Implemented |
| 2   | Firestore security rules deny all client writes to audit collections | Design  | PRS-009    | Implemented |
| 3   | Audit log viewing and filtering for proactive completeness checks    | Design  | PRS-010    | Implemented |
| 4   | Every mutation path creates an audit entry (centralized logging)     | Design  | PRS-009    | Implemented |
| 5   | Indefinite retention by default with minimum 2-year retention policy | Design  | PRS-009    | Implemented |
| 6   | Management review SOP includes periodic audit trail review           | Process | SOP-010   | Implemented |

## 6. Verification of Mitigation

| Mitigation # | Verification Method                  | Evidence                               | Result  |
| ------------ | ------------------------------------ | -------------------------------------- | ------- |
| 1            | Unit test — immutability enforcement | Entry modification rejection tests     | Pending |
| 2            | Firestore security rule tests        | Client write rejection tests           | Pending |
| 3            | E2E test — audit log viewing         | Filter and view functionality tests    | Pending |
| 4            | Integration test — coverage          | Audit entry for each mutation endpoint | Pending |
| 5            | Configuration review                 | Retention policy configuration         | Pending |
| 6            | Process audit                        | SOP-010 compliance records             | Pending |

## 7. Residual Risk Acceptability

Residual risk is **acceptable**. The immutable audit trail architecture (server-generated timestamps, Firestore security rules, centralized Cloud Functions logging) makes both omission and tampering extremely difficult. The audit log viewing feature (PRS-010) enables proactive verification of completeness before audits. The management review SOP (SOP-010) provides a periodic process check to validate audit trail health.

## 8. Traceability

| Direction | Link Type    | Target ID     | Title                         |
| --------- | ------------ | ------------- | ----------------------------- |
| Up        | derives-from | RISK-SW-003   | Incorrect Audit Trail         |
| Down      | leads-to     | HARM-001 | Regulatory Non-Compliance     |
| Down      | mitigated-by | PRS-009        | Audit Log Capture             |
| Down      | mitigated-by | PRS-010        | Audit Log Viewing & Filtering |
| Down      | mitigated-by | SOP-010       | Management Review             |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
