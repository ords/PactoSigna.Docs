---
id: RISK-MATRIX-001
title: 'Risk Matrix Summary'
status: draft
links:
  - type: summarizes
    target: RISK-SW-001
  - type: summarizes
    target: RISK-SW-002
  - type: summarizes
    target: RISK-SW-003
  - type: summarizes
    target: RISK-SW-004
  - type: summarizes
    target: RISK-SW-005
  - type: summarizes
    target: RISK-SW-006
  - type: summarizes
    target: RISK-SEC-001
  - type: summarizes
    target: RISK-SEC-002
  - type: summarizes
    target: RISK-SEC-003
  - type: summarizes
    target: RISK-SEC-004
  - type: summarizes
    target: RISK-SEC-005
  - type: summarizes
    target: HS-001
  - type: summarizes
    target: HS-002
  - type: summarizes
    target: HS-003
  - type: summarizes
    target: HARM-001
  - type: summarizes
    target: HARM-002
  - type: related-to
    target: ISSUE-184
  - type: related-to
    target: ISSUE-185
---

# Risk Matrix Summary

## 1. Purpose

Provide a single, auditable summary view of all current software, security, hazardous situation, and harm risk records.

## 2. Scale Reference

- Severity: 1 (Low) to 5 (Catastrophic)
- Probability: 1 (Remote) to 5 (Frequent)
- Detectability: 1 (High detectability) to 5 (Low detectability)
- `N/A` indicates the source document does not define that field.

## 3. Consolidated Matrix

| Risk ID       | Category            | Title                                                     | Severity (Pre) | Probability (Pre) | Detectability (Pre) | Residual (Post) | Residual Status                                       | Source                                                                  |
| ------------- | ------------------- | --------------------------------------------------------- | -------------- | ----------------- | ------------------- | --------------- | ----------------------------------------------------- | ----------------------------------------------------------------------- |
| RISK-SW-001   | Software            | Document Sync Data Loss                                   | 4              | 3                 | 3                   | Low (RPN 4)     | Acceptable                                            | docs/risk/software/RISK-SW-001-document-sync-data-loss.md               |
| RISK-SW-002   | Software            | Electronic Signature Bypass                               | 5              | 2                 | 2                   | Low (RPN 5)     | Acceptable                                            | docs/risk/software/RISK-SW-002-signature-bypass.md                      |
| RISK-SW-003   | Software            | Incorrect Audit Trail                                     | 4              | 3                 | 3                   | Low (RPN 4)     | Acceptable                                            | docs/risk/software/RISK-SW-003-incorrect-audit-trail.md                 |
| RISK-SW-004   | Software            | Release with Unsigned Documents                           | 5              | 2                 | 2                   | Low (RPN 5)     | Acceptable                                            | docs/risk/software/RISK-SW-004-release-unsigned-documents.md            |
| RISK-SW-005   | Software            | Training Assignment Failure                               | 4              | 3                 | 3                   | Medium (RPN 8)  | Acceptable with monitoring                            | docs/risk/software/RISK-SW-005-training-assignment-failure.md           |
| RISK-SW-006   | Software            | Incorrect Traceability                                    | 4              | 3                 | 4                   | Medium (RPN 16) | Acceptable with monitoring                            | docs/risk/software/RISK-SW-006-incorrect-traceability.md                |
| RISK-SEC-001  | Security            | Penetration Testing Plan                                  | N/A            | N/A               | N/A                 | N/A             | Open assessment artifact (ratings not yet formalized) | docs/risk/security/RISK-SEC-001-penetration-testing.md                  |
| RISK-SEC-002  | Security            | Authentication Bypass                                     | 5              | 2                 | 3                   | Low (RPN 5)     | Acceptable                                            | docs/risk/security/RISK-SEC-002-authentication-bypass.md                |
| RISK-SEC-003  | Security            | Webhook Tampering                                         | 4              | 3                 | 3                   | Low (RPN 4)     | Acceptable                                            | docs/risk/security/RISK-SEC-003-webhook-tampering.md                    |
| RISK-SEC-004  | Security            | Token Exposure                                            | 5              | 2                 | 4                   | Medium (RPN 10) | Acceptable with monitoring                            | docs/risk/security/RISK-SEC-004-token-exposure.md                       |
| RISK-SEC-005  | Security            | Privilege Escalation                                      | 4              | 2                 | 2                   | Low (RPN 4)     | Acceptable                                            | docs/risk/security/RISK-SEC-005-privilege-escalation.md                 |
| HS-001  | Hazardous Situation | Regulatory Submission with Incomplete Traceability Matrix | 4              | 3                 | 3                   | Medium (RPN 8)  | Acceptable with monitoring                            | docs/risk/situations/HS-001-incomplete-traceability-submission.md |
| HS-002  | Hazardous Situation | Audit with Tampered or Missing Audit Logs                 | 4              | 2                 | 3                   | Low (RPN 4)     | Acceptable                                            | docs/risk/situations/HS-002-audit-missing-logs.md                 |
| HS-003  | Hazardous Situation | Released Software Without Required Training Completion    | 4              | 3                 | 3                   | Medium (RPN 8)  | Acceptable with monitoring                            | docs/risk/situations/HS-003-released-without-training.md          |
| HARM-001 | Harm                | Regulatory Non-Compliance                                 | 5              | 2                 | N/A                 | Low             | Acceptable                                            | docs/risk/harms/HARM-001-regulatory-non-compliance.md              |
| HARM-002 | Harm                | Patient Safety Impact from Non-Compliant Device Software  | 5              | 1                 | N/A                 | Low             | Acceptable                                            | docs/risk/harms/HARM-002-patient-safety-impact.md                  |

## 4. Review Cadence

- Update this matrix whenever a `RISK-*` document is added, removed, or has a changed residual conclusion.
- Review at least once per release cycle and during management review (SOP-010).

## Revision History

| Version | Date       | Author          | Changes                                         |
| ------- | ---------- | --------------- | ----------------------------------------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial matrix summarizing current risk records |
