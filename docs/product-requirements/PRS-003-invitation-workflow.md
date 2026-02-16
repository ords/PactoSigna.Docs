---
id: PRS-003
title: 'Invitation Workflow'
status: draft
links:
  - type: derives-from
    target: UN-001
---

# Invitation Workflow

## Requirement Statements

1. The system **SHALL** allow Owners and Admins to invite users to the organization by email address, specifying a role (Admin or Member) and department assignment.

2. The system **SHALL** send an email notification to each invitee containing a secure, time-limited invitation link.

3. The system **SHALL** allow invitees to accept an invitation and join the organization regardless of whether they already have a PactoSigna account (new users create an account during acceptance).

4. The system **SHALL** allow Owners and Admins to revoke a pending invitation before it is accepted.

5. The system **SHALL** automatically expire invitations that are not accepted within a configurable time period (default: 7 days).

6. The system **SHALL** prevent duplicate invitations to the same email address within the same organization while a pending invitation exists.

7. The system **SHALL** record every invitation event (send, accept, revoke, expire) as an immutable audit log entry.

## Rationale

ISO 13485 §6.2 requires that personnel performing work affecting product quality are competent and that records of education, training, skills, and experience are maintained. The invitation workflow is the entry point for personnel into the QMS — proper controls ensure only authorized individuals gain access, every access grant is traceable, and the audit trail captures the complete membership lifecycle from invitation through acceptance.

## Classification

| Field    | Value                                        |
| -------- | -------------------------------------------- |
| Priority | Essential                                    |
| Category | Functional                                   |
| Standard | ISO 13485 §6.2, FDA 21 CFR Part 11 §11.10(d) |

## Acceptance Criteria

1. Given an Admin, when they invite a user by email with role and department, then an invitation record is created and an email is sent with a secure link
2. Given a user who does not have a PactoSigna account, when they click the invitation link, then they are prompted to create an account and upon completion are added to the organization with the specified role and department
3. Given a user who already has a PactoSigna account, when they click the invitation link and are authenticated, then they are added to the organization immediately
4. Given a pending invitation, when an Admin revokes it, then the invitation link becomes invalid and an audit log entry is created
5. Given an invitation older than the configured expiry period, then the invitation is automatically marked as expired and the link becomes invalid
6. Given an email address with an existing pending invitation in the same organization, when an Admin attempts to re-invite that email, then the system rejects the duplicate with an error message

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
