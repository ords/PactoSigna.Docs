---
id: SOP-009
title: 'Corrective & Preventive Action (CAPA)'
status: draft
links:
  - SOP-004
  - SOP-006
  - SOP-010
---

# Corrective & Preventive Action (CAPA)

## 1. Purpose

Establish the process for identifying, investigating, correcting, and preventing nonconformities in PactoSigna's software and QMS processes, in compliance with ISO 13485 §8.5.

## 2. Scope

Applies to all nonconformities identified through any source: bug reports, audit findings, complaint investigations, failed tests, production incidents, or management review outputs. Covers all team members responsible for quality and development.

## 3. Definitions

| Term                | Definition                                                             |
| ------------------- | ---------------------------------------------------------------------- |
| Nonconformity       | Failure to meet a specified requirement                                |
| Corrective Action   | Action to eliminate the root cause of a detected nonconformity         |
| Preventive Action   | Action to eliminate the cause of a potential nonconformity             |
| Root Cause Analysis | Systematic investigation to identify the fundamental cause of an issue |
| CAPA                | The combined corrective and preventive action process                  |

## 4. Responsibilities

| Role            | Responsibility                                                     |
| --------------- | ------------------------------------------------------------------ |
| Anyone          | Reports nonconformities via GitHub Issues                          |
| Quality Manager | Opens CAPA records, oversees investigation, verifies effectiveness |
| Developer       | Performs root cause analysis and implements corrective actions     |
| Team Lead       | Reviews CAPA status, ensures timely closure                        |

## 5. Procedure

### 5.1 Identification

1. Create a GitHub Issue for the nonconformity with the label `capa`.
2. Include: description of the nonconformity, how it was detected (bug report, audit, test failure, production log), and immediate impact assessment.
3. If the issue poses an immediate risk, implement a containment action (e.g., feature flag, rollback) and document it in the issue.

### 5.2 Investigation and Root Cause Analysis

1. Assign the issue to a developer for investigation.
2. Perform root cause analysis using available evidence: audit logs (Firestore), error logs, git history, and reproduction steps.
3. Document the root cause in the GitHub Issue with supporting evidence.
4. Assess whether the root cause could affect other areas (risk of recurrence).

### 5.3 Corrective Action

1. Plan the corrective action: what code, document, or process change will eliminate the root cause.
2. Implement the fix following SOP-005 (feature branch, PR, CI, review).
3. Add regression tests that specifically verify the fix (test-first approach preferred).
4. The PR must reference the CAPA issue (`Closes #<number>`).

### 5.4 Preventive Action

1. If root cause analysis reveals a systemic issue, define a preventive action.
2. Preventive actions may include: process updates (SOP revisions), additional automated checks (lint rules, CI gates), or architectural improvements (ADRs).
3. Document preventive actions in the GitHub Issue.
4. Implement preventive actions following the standard change control process (SOP-006).

### 5.5 Effectiveness Review

1. After the corrective action is released, monitor for recurrence over the next two release cycles.
2. Review production logs, audit trails, and test results for evidence of effectiveness.
3. The Quality Manager documents the effectiveness review outcome in the GitHub Issue.
4. Close the CAPA issue only after effectiveness is confirmed.
5. If the corrective action is not effective, reopen and repeat from §5.2.

## 6. Records

| Record                | Retention       | Location              |
| --------------------- | --------------- | --------------------- |
| CAPA GitHub Issues    | Life of product | GitHub Issues         |
| Root cause analysis   | Life of product | GitHub Issue comments |
| Corrective action PRs | Life of product | GitHub Pull Requests  |
| Effectiveness reviews | Life of product | GitHub Issue comments |
| Audit log entries     | 3 years         | Firestore (auditLog)  |

## 7. References

- ISO 13485:2016 §8.5.2 — Corrective Action
- ISO 13485:2016 §8.5.3 — Preventive Action
- SOP-004 — Risk Management
- SOP-006 — Change Control
- SOP-010 — Management Review

## Revision History

| Version | Date       | Author        | Changes       |
| ------- | ---------- | ------------- | ------------- |
| 0.1     | 2026-02-15 | PactoSigna AI | Initial draft |
