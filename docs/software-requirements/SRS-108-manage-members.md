---
id: SRS-108
title: 'Manage Members'
status: draft
links:
  - type: derives-from
    target: PRS-003
---

# Manage Members

## Requirement

> **SRS-108.1:** The system **SHALL** display a member list showing name, email, department, role, and joined date, with the ability to filter and search by name, email, or department.
>
> **SRS-108.2:** The system **SHALL** allow admins to change a member's department and role (admin / member / auditor).
>
> **SRS-108.3:** The system **SHALL** allow admins to remove members from the organization, while preserving the member's Firebase Auth account.
>
> **SRS-108.4:** The system **SHALL** prevent an admin from removing themselves or demoting themselves from admin if they are the last admin in the organization.
>
> **SRS-108.5:** The system **SHALL** create an audit log entry for all member management actions (role change, department change, removal).

## Context

Member management is restricted to admin users. The `updateMember` and `removeMember` Cloud Functions handle mutations. The `MemberRoleChanged` domain event is fired on role changes. The system enforces a business rule that at least one admin must always exist in the organization to prevent lockout scenarios.

## Classification

| Field           | Value             |
| --------------- | ----------------- |
| Safety Class    | A                 |
| Category        | Functional        |
| Bounded Context | Identity & Access |

## Detailed Specification

### Inputs

- Member ID (for update or removal)
- New department (for department change)
- New role (admin / member / auditor, for role change)

### Processing

1. Validate admin authorization.
2. For role/department changes: update the member document.
3. For removal: soft-remove by updating access (member loses org access but keeps auth account).
4. Check last-admin constraint before demotion or removal.
5. Fire `MemberRoleChanged` domain event.
6. Create audit log entry.

### Outputs

- Updated or removed member document
- Audit log entry for each action

### Error Handling

- Last admin demotion attempt: reject with "Cannot demote the last admin"
- Last admin removal attempt: reject with "Cannot remove the last admin"
- Non-admin access: return 403 Forbidden

## Acceptance Criteria

1. **Given** an admin on the member list, **when** they view the list, **then** all members are displayed with name, email, department, role, and joined date.
2. **Given** an admin, **when** they change a member's role, **then** the role is updated and an audit log entry is created.
3. **Given** an admin, **when** they remove a member, **then** the member loses organization access but retains their Firebase Auth account.
4. **Given** a sole admin, **when** they attempt to demote themselves, **then** the action is rejected with an error message.
5. **Given** an admin, **when** they search by name or email, **then** the member list is filtered accordingly.

## Traceability

| Direction | Link Type    | Target ID | Title               |
| --------- | ------------ | --------- | ------------------- |
| Up        | derives-from | PRS-003    | Invitation workflow |

## Source

**STORY-108 — Manage Members**

> **As an** organization admin **I want** to manage organization members **So that** I can update roles and remove departed team members.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
