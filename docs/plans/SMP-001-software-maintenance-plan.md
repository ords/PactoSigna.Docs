---
id: SMP-001
title: 'Software Maintenance Plan'
status: draft
links:
  - type: related-to
    target: SOP-001
  - type: related-to
    target: SOP-005
  - type: related-to
    target: SOP-007
  - type: related-to
    target: SOP-009
  - type: related-to
    target: SOP-010
  - type: related-to
    target: ISSUE-184
  - type: related-to
    target: ISSUE-185
---

# Software Maintenance Plan

## 1. Purpose

Define how PactoSigna software is maintained after initial release, including defect handling, security maintenance, dependency upkeep, and evidence retention.

## 2. Scope

- Applies to all monorepo workspaces (`apps/*`, `packages/*`, shared docs/process artifacts).
- Covers corrective, adaptive, and preventive maintenance.

## 3. Maintenance Inputs

- GitHub issues (single source of truth for work items)
- Customer feedback and support reports
- CI failures and regression signals
- Security advisories and dependency vulnerability notifications
- Audit findings and CAPA actions

## 4. Maintenance Workflow

1. **Intake**
   - Create or refine a GitHub issue with problem statement, impact, and acceptance criteria.
   - Label by type (`bug`, `tech-debt`, `compliance`, `security`) and priority.
2. **Triage**
   - Assess severity, scope, and regulatory/compliance impact.
   - Identify bounded context and linked risks/SOPs.
3. **Implementation**
   - Deliver via PR with focused change scope and review evidence.
   - Maintain traceability from issue -> PR -> changed artifacts.
4. **Verification**
   - Run required checks (`pnpm lint`, `pnpm typecheck`, `pnpm test`, targeted e2e as needed).
   - Confirm acceptance criteria are met and regressions are absent.
5. **Release and Closure**
   - Merge through normal PR process.
   - Record release notes and close issue with evidence references.

## 5. Maintenance Types and Expected SLAs

| Type       | Example                                                 | Target Response              | Target Resolution                         |
| ---------- | ------------------------------------------------------- | ---------------------------- | ----------------------------------------- |
| Corrective | Production bug impacting document/release/training flow | 1 business day               | 5 business days                           |
| Security   | Auth bypass, token exposure, webhook abuse risk         | Same day triage              | Based on severity (expedite critical)     |
| Preventive | Refactors, reliability hardening, tech debt             | Planned sprint intake        | Planned per backlog priority              |
| Adaptive   | Platform/API compatibility updates                      | Planned on dependency change | Before incompatibility reaches production |

## 6. Roles and Responsibilities

| Role               | Responsibility                                     |
| ------------------ | -------------------------------------------------- |
| Product Manager    | Define acceptance criteria and priority            |
| Engineering        | Implement fixes, tests, and technical verification |
| Quality/Regulatory | Confirm compliance impact and required evidence    |
| Reviewer/Approver  | Ensure PR quality gates pass before merge          |

## 7. Verification and Evidence

Minimum maintenance evidence per completed item:

- Linked issue and PR
- Test/lint/typecheck results in CI
- Updated risk/SOP/doc artifacts where applicable
- Audit-relevant rationale for compliance-affecting changes

## 8. Continuous Maintenance Cadence

- Weekly: triage open bugs/security issues.
- Per release cycle: review unresolved medium/high risks and dependency status.
- Quarterly: maintenance effectiveness review during management review.

## 9. Exit Criteria per Maintenance Item

A maintenance item is complete when:

- acceptance criteria are met,
- CI quality gates pass,
- traceability artifacts are linked,
- compliance impacts are documented.

## Revision History

| Version | Date       | Author          | Changes                  |
| ------- | ---------- | --------------- | ------------------------ |
| 0.1     | 2026-02-15 | Product Manager | Initial maintenance plan |
