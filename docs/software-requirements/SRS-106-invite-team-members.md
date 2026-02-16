---
id: SRS-106
title: 'Invite Team Members'
status: draft
links:
  - type: derives-from
    target: PRS-003
---

# Invite Team Members

## Requirement

> **SRS-106.1:** The system **SHALL** provide an invite form that collects the invitee's email address, department, and role (admin / member / auditor).
>
> **SRS-106.2:** The system **SHALL** send an invitation email containing a unique join link that expires after 7 days (configurable).
>
> **SRS-106.3:** The system **SHALL** allow admins to view pending invites, revoke pending invites, and resend invitation emails.
>
> **SRS-106.4:** The system **SHALL** prevent inviting an email address that is already a member of the organization.
>
> **SRS-106.5:** The system **SHALL** create an audit log entry for each invite action via the `inviteUser` Cloud Function.

## Context

Team member invitation is a core onboarding workflow. The Cloud Function `inviteUser` generates a secure random token, stores the invite in `/organizations/{orgId}/invites/{inviteId}`, and sends an email. The `MemberInvited` domain event is fired. Invite links are time-limited for security.

## Classification

| Field           | Value             |
| --------------- | ----------------- |
| Safety Class    | A                 |
| Category        | Functional        |
| Bounded Context | Identity & Access |

## Detailed Specification

### Inputs

- Invitee email address (valid email format)
- Department (from organization's department list)
- Role (admin / member / auditor)

### Processing

1. Validate admin authorization.
2. Check that the email is not already an active member.
3. Generate secure random invite token.
4. Store invite document in Firestore with expiration timestamp.
5. Send invitation email with join link.
6. Fire `MemberInvited` domain event.
7. Create audit log entry.

### Outputs

- Invite document in Firestore
- Invitation email sent to the invitee
- Audit log entry

### Error Handling

- Email already a member: display "This email is already a member of the organization"
- Invalid email format: display validation error
- Email sending failure: log error, allow retry

## Acceptance Criteria

1. **Given** an admin on the invite form, **when** they submit a valid email, department, and role, **then** an invite is created and an email is sent.
2. **Given** an admin, **when** they attempt to invite an email that is already a member, **then** the invite is rejected with an error message.
3. **Given** an admin viewing pending invites, **when** they click revoke, **then** the invite is cancelled and cannot be accepted.
4. **Given** an admin viewing pending invites, **when** they click resend, **then** a new invitation email is sent.
5. **Given** an invite that is 8 days old, **when** the invitee attempts to accept, **then** the invite is rejected as expired.

## Traceability

| Direction | Link Type    | Target ID | Title               |
| --------- | ------------ | --------- | ------------------- |
| Up        | derives-from | PRS-003    | Invitation workflow |

## Source

**STORY-106 — Invite Team Members**

> **As an** organization admin **I want** to invite team members to my organization **So that** my team can collaborate on QMS documents.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
