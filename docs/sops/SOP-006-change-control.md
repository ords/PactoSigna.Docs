---
id: SOP-006
title: 'Change Control'
status: draft
links:
  - SOP-002
  - SOP-005
---

# Change Control

## 1. Purpose

Define the process for requesting, evaluating, approving, and implementing changes to PactoSigna software and documentation, ensuring changes are controlled and traceable per ISO 13485 §7.3.9.

## 2. Scope

Applies to all changes to released software, controlled documents, and infrastructure. Covers all team members who initiate, review, or approve changes.

## 3. Definitions

| Term            | Definition                                                                |
| --------------- | ------------------------------------------------------------------------- |
| Change Request  | A GitHub Issue describing a proposed change with rationale                |
| Impact Analysis | Assessment of the effect of a change on requirements, risk, and testing   |
| Change Control  | PactoSigna entity linking a Release to the changes and approvals required |
| Breaking Change | A change that alters existing behavior or API contracts                   |

## 4. Responsibilities

| Role            | Responsibility                                                    |
| --------------- | ----------------------------------------------------------------- |
| Requestor       | Creates a GitHub Issue describing the change and rationale        |
| Product Manager | Evaluates priority and accepts/rejects the change request         |
| Developer       | Performs impact analysis and implements the approved change       |
| Quality Manager | Reviews impact analysis, approves change, verifies implementation |

## 5. Procedure

### 5.1 Change Request

1. Create a GitHub Issue with the label `change-request` describing: what is changing, why, and the expected benefit.
2. Include references to affected documents, requirements, or risk items.
3. The issue enters the project board for triage.

### 5.2 Impact Analysis

1. The assigned developer documents the impact analysis in the GitHub Issue, covering:
   - Affected software components (apps, packages)
   - Affected documents (requirements, architecture, risk)
   - Regression risk and required test updates
   - Regulatory impact (Part 11, IEC 62304 classification)
2. For changes affecting risk controls, update the risk register (SOP-004).

### 5.3 Change Approval

1. The Quality Manager reviews the impact analysis.
2. For low-impact changes (documentation-only, minor bug fixes): a single PR approval suffices.
3. For high-impact changes (architecture, risk controls, regulatory features): require explicit approval from Quality Manager before implementation begins.
4. Record the approval decision in the GitHub Issue comments.

### 5.4 Implementation

1. Implement the change following SOP-005 (feature branch, PR, CI, review).
2. The PR description must reference the change request issue (`Closes #<number>`).
3. Update all affected documents, requirements, and risk artifacts in the same PR or linked PRs.

### 5.5 Verification and Closure

1. Verify the change through testing per SOP-007.
2. Confirm all impacted documents have been updated.
3. Include the change in a Release with appropriate electronic signatures.
4. Close the change request issue upon successful release.

## 6. Records

| Record           | Retention       | Location               |
| ---------------- | --------------- | ---------------------- |
| Change request   | Life of product | GitHub Issues          |
| Impact analysis  | Life of product | GitHub Issue comments  |
| Approval records | Life of product | GitHub PR reviews      |
| Release records  | Life of product | PactoSigna (Firestore) |

## 7. References

- ISO 13485:2016 §7.3.9 — Control of Design and Development Changes
- SOP-002 — Document Control
- SOP-005 — Software Development Lifecycle

## Revision History

| Version | Date       | Author        | Changes       |
| ------- | ---------- | ------------- | ------------- |
| 0.1     | 2026-02-15 | PactoSigna AI | Initial draft |
