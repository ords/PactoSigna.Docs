---
id: HARM-001
title: 'Regulatory Non-Compliance'
status: draft
links:
  - type: derives-from
    target: HS-001
  - type: derives-from
    target: HS-002
  - type: derives-from
    target: HS-003
---

# Regulatory Non-Compliance

## 1. Purpose

This document describes the harm of regulatory non-compliance arising from hazardous situations identified in the PactoSigna risk analysis. As a QMS SaMD used by medical device companies, failures in PactoSigna's compliance features can directly cause regulatory consequences for its users' organizations.

## 2. Risk Category

| Field       | Value                                                    |
| ----------- | -------------------------------------------------------- |
| Category    | Harm                                                     |
| Subcategory | Regulatory — compliance enforcement actions              |
| Standard    | ISO 14971, ISO 13485, FDA 21 CFR Part 11, FDA 21 CFR 820 |

## 3. Hazard Identification

### 3.1 Hazard Description

Organizations using PactoSigna for their QMS suffer regulatory enforcement actions (FDA Warning Letters, 483 observations, CE mark withdrawal, Notified Body audit failure) because the QMS software failed to ensure compliance. This harm materializes when PactoSigna's compliance controls — traceability, audit trails, electronic signatures, training records — do not function correctly, and the organization relies on them for regulatory submissions and audit evidence.

### 3.2 Foreseeable Sequence of Events

1. A hazardous situation occurs (HS-001, HS-002, or HS-003)
2. The organization relies on PactoSigna's output for regulatory compliance
3. A regulatory body discovers compliance deficiencies during inspection or submission review
4. The regulatory body issues enforcement actions:
   - FDA: Warning Letter, 483 observation, consent decree, import alert
   - EU: CE mark suspension/withdrawal, Notified Body non-conformity
5. Organization faces business consequences: delayed market access, product recall, loss of market authorization

### 3.3 Affected Users / Stakeholders

- The customer organization facing enforcement actions
- QA Managers and Regulatory Specialists whose professional credibility is affected
- Patients who lose access to beneficial medical devices during enforcement periods
- PactoSigna as a vendor whose product contributed to the compliance failure

## 4. Risk Estimation

### Before Mitigation

| Factor         | Rating           | Justification                                                                              |
| -------------- | ---------------- | ------------------------------------------------------------------------------------------ |
| Severity       | 5 — Catastrophic | Warning letters and CE mark withdrawal can halt business operations and market access      |
| Probability    | 2 — Unlikely     | Requires multiple upstream failures; regulatory bodies provide opportunity for remediation |
| **Risk Level** | **High**         | Maximum severity with unlikely probability; unacceptable without mitigation                |

### After Mitigation

| Factor            | Rating           | Justification                                                                                       |
| ----------------- | ---------------- | --------------------------------------------------------------------------------------------------- |
| Severity          | 5 — Catastrophic | Severity of regulatory enforcement remains inherently catastrophic                                  |
| Probability       | 1 — Remote       | Upstream mitigations (design + process controls) across all contributing risks minimize probability |
| **Residual Risk** | **Low**          | Upstream risk controls reduce all contributing hazardous situations to acceptable levels            |

## 5. Risk Mitigation

Harm-level mitigations are indirect — they are achieved through mitigating the upstream hazardous situations:

| #   | Mitigation Measure                                       | Type    | Target ID | Status      |
| --- | -------------------------------------------------------- | ------- | --------- | ----------- |
| 1   | Traceability gap detection and coverage reporting        | Design  | PRS-021    | Planned     |
| 2   | Immutable audit trail with server-generated timestamps   | Design  | PRS-009    | Implemented |
| 3   | Automated training assignment with overdue monitoring    | Design  | PRS-016    | Implemented |
| 4   | Electronic signature enforcement with re-authentication  | Design  | PRS-014    | Implemented |
| 5   | Release workflow with signature completeness validation  | Design  | PRS-012    | Implemented |
| 6   | Quality Management System SOPs (SOP-001 through SOP-010) | Process | SOP-004   | Implemented |
| 7   | Management review with compliance metrics                | Process | SOP-010   | Implemented |

## 6. Verification of Mitigation

Verification is performed at the upstream risk level. See:

| Upstream Risk | Verification Reference                 |
| ------------- | -------------------------------------- |
| HS-001  | Traceability completeness verification |
| HS-002  | Audit trail integrity verification     |
| HS-003  | Training completion verification       |

## 7. Residual Risk Acceptability

Residual risk is **acceptable**. All three contributing hazardous situations have been mitigated to acceptable residual risk levels through a combination of design controls (automated detection, enforcement, monitoring) and process controls (SOPs, management review). The overall benefit-risk analysis is favorable: PactoSigna provides significantly stronger compliance automation than manual QMS processes, making the residual risk of regulatory non-compliance substantially lower than the baseline risk of operating without such a system.

## 8. Traceability

| Direction | Link Type    | Target ID    | Title                                              |
| --------- | ------------ | ------------ | -------------------------------------------------- |
| Up        | derives-from | HS-001 | Regulatory Submission with Incomplete Traceability |
| Up        | derives-from | HS-002 | Audit with Tampered or Missing Audit Logs          |
| Up        | derives-from | HS-003 | Released Software Without Required Training        |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
