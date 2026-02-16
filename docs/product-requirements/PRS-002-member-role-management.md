---
id: PRS-002
title: 'Member Role Management'
status: draft
links:
  - type: derives-from
    target: UN-001
---

# Member Role Management

## Requirement Statements

1. The system **SHALL** support three distinct member roles within an organization: Owner, Admin, and Member, each with defined permission boundaries.

2. The system **SHALL** enforce role-based access control such that only Owners and Admins can perform administrative actions (invite members, manage roles, configure settings, create releases).

3. The system **SHALL** allow Owners and Admins to change a member's role, subject to the constraint that at least one Owner must exist at all times.

4. The system **SHALL** allow Owners and Admins to update a member's department assignment.

5. The system **SHALL** allow Owners and Admins to remove members from the organization, except when removal would leave the organization with no Owner.

6. The system **SHALL** prevent a member from escalating their own role beyond their current permission level.

7. The system **SHALL** record every member role change, department assignment, and removal as an immutable audit log entry.

## Rationale

FDA 21 CFR Part 11 requires unique user identification and access controls that limit system access to authorized individuals. ISO 13485 §5.5.1 requires that responsibilities, authorities, and their interrelation are defined and communicated. A well-defined role hierarchy ensures that sensitive QMS operations (releases, signatures, configuration) are restricted to authorized personnel, while members retain appropriate read and contribute access.

## Classification

| Field    | Value                                          |
| -------- | ---------------------------------------------- |
| Priority | Essential                                      |
| Category | Functional / Regulatory                        |
| Standard | FDA 21 CFR Part 11 §11.10(d), ISO 13485 §5.5.1 |

## Acceptance Criteria

1. Given a Member-role user, when they attempt to invite another member or change organization settings, then the system denies the action with a 403 Forbidden response
2. Given an Owner, when they attempt to change the last remaining Owner to Admin or Member, then the system rejects the change with an error indicating at least one Owner is required
3. Given an Admin, when they change a member's department, then the member's department is updated and an audit log entry is created
4. Given an Admin removing a member, when that member has pending training tasks, then the system allows removal and marks pending tasks as cancelled
5. Given any role or department change, then the audit log entry includes the acting user, target member, previous value, new value, and server-generated timestamp

## Traceability

| Direction | Link Type    | Target ID | Title                       |
| --------- | ------------ | --------- | --------------------------- |
| Up        | derives-from | UN-001    | Organization & Team Setup   |
| Down      | refined-by   | —         | _(To be linked by SWR-xxx)_ |
| Down      | verified-by  | —         | _(To be linked by TP-xxx)_  |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
