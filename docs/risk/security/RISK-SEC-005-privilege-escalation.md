---
id: RISK-SEC-005
title: 'Privilege Escalation'
status: draft
links:
  - type: derives-from
    target: PRS-002
  - type: mitigated-by
    target: SOP-005
---

# Privilege Escalation

## 1. Purpose

This document assesses the risk of a member with limited permissions (Member role) gaining administrative or owner-level privileges within the QMS. Privilege escalation undermines access controls mandated by FDA 21 CFR Part 11 §11.10(d) and ISO 13485 §5.5.1.

## 2. Risk Category

| Field       | Value                                                     |
| ----------- | --------------------------------------------------------- |
| Category    | Security                                                  |
| Subcategory | Authorization — privilege escalation                      |
| Standard    | ISO 14971, FDA 21 CFR Part 11 §11.10(d), ISO 13485 §5.5.1 |

## 3. Hazard Identification

### 3.1 Hazard Description

A member may escalate their own role by: (a) directly calling API endpoints that modify member roles without proper authorization checks, (b) exploiting a defect in the role-change validation logic, (c) manipulating Firestore documents directly (bypassing Cloud Functions), or (d) exploiting race conditions in concurrent role-change operations. Escalated privileges would grant access to create releases, manage members, configure organization settings, and perform other administrative actions.

### 3.2 Foreseeable Sequence of Events

1. A Member identifies an API endpoint or Firestore path that modifies role assignments
2. The Member exploits insufficient authorization checks to change their own role to Admin or Owner
3. With elevated privileges, the Member performs unauthorized actions (creates releases, changes settings, modifies other members' roles)
4. Unauthorized changes compromise QMS data integrity and release processes
5. Audit trail shows actions performed by a user who should not have had permission

### 3.3 Affected Users / Stakeholders

- Organization Owners/Admins whose authority is undermined
- All members affected by unauthorized configuration changes
- QA Managers whose release workflows may be compromised
- The organization facing compliance violations

## 4. Risk Estimation

### Before Mitigation

| Factor         | Rating               | Justification                                                                                      |
| -------------- | -------------------- | -------------------------------------------------------------------------------------------------- |
| Severity       | 4 — Major            | Escalated privileges enable unauthorized releases, member changes, and configuration modifications |
| Probability    | 2 — Unlikely         | Requires finding specific authorization gaps in server-side code                                   |
| Detectability  | 2 — Good             | Role changes are logged in audit trail; unauthorized elevated actions would be visible             |
| **Risk Level** | **Medium (RPN: 16)** | 4 × 2 × 2                                                                                          |

### After Mitigation

| Factor            | Rating           | Justification                                                                                      |
| ----------------- | ---------------- | -------------------------------------------------------------------------------------------------- |
| Severity          | 4 — Major        | Severity of privilege escalation remains high                                                      |
| Probability       | 1 — Remote       | Server-side RBAC enforcement, no client writes, and self-escalation prevention minimize likelihood |
| Detectability     | 1 — High         | Every role change logged with actor identity; anomalous changes immediately visible                |
| **Residual Risk** | **Low (RPN: 4)** | 4 × 1 × 1                                                                                          |

## 5. Risk Mitigation

| #   | Mitigation Measure                                                 | Type    | Target ID | Status      |
| --- | ------------------------------------------------------------------ | ------- | --------- | ----------- |
| 1   | Server-side RBAC enforcement — only Owners/Admins can change roles | Design  | PRS-002    | Implemented |
| 2   | Self-escalation prevention — members cannot elevate their own role | Design  | PRS-002    | Implemented |
| 3   | At least one Owner must exist at all times (Owner removal guard)   | Design  | PRS-002    | Implemented |
| 4   | Firestore security rules deny all client-side writes               | Design  | PRS-002    | Implemented |
| 5   | Every role change and member modification logged in audit trail    | Design  | PRS-009    | Implemented |
| 6   | Code review for authorization enforcement on all API endpoints     | Process | SOP-005   | Implemented |

## 6. Verification of Mitigation

| Mitigation # | Verification Method                 | Evidence                            | Result  |
| ------------ | ----------------------------------- | ----------------------------------- | ------- |
| 1            | Integration test — role change auth | Member role-change rejection tests  | Pending |
| 2            | Integration test — self-escalation  | Self-role-change rejection tests    | Pending |
| 3            | Unit test — Owner guard             | Last-Owner removal prevention tests | Pending |
| 4            | Firestore security rule tests       | Client write rejection tests        | Pending |
| 5            | Integration test — audit logging    | Role change audit entry tests       | Pending |
| 6            | Process audit                       | Code review records                 | Pending |

## 7. Residual Risk Acceptability

Residual risk is **acceptable**. The backend-only-writes architecture (ADR-008) eliminates the entire class of client-side Firestore manipulation attacks. Server-side RBAC enforcement with explicit self-escalation prevention addresses the API-level attack vector. The comprehensive audit trail ensures that any successful privilege escalation (however unlikely) would be immediately detectable through role change audit entries.

## 8. Traceability

| Direction | Link Type    | Target ID | Title                          |
| --------- | ------------ | --------- | ------------------------------ |
| Up        | derives-from | PRS-002    | Member Role Management         |
| Down      | mitigated-by | SOP-005   | Software Development Lifecycle |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
