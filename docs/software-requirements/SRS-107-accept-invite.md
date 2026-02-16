---
id: SRS-107
title: 'Accept Invite'
status: draft
links:
  - type: derives-from
    target: PRS-003
---

# Accept Invite

## Requirement

> **SRS-107.1:** The system **SHALL** allow both existing users (login then join) and new users (signup then join) to accept an invitation via the invite link.
>
> **SRS-107.2:** The system **SHALL** validate the invite token and expiration before allowing acceptance, and display a clear error message for invalid or expired invites.
>
> **SRS-107.3:** The system **SHALL** create a member document with the pre-assigned department and role upon invite acceptance, and mark the invite as accepted so it cannot be reused.
>
> **SRS-107.4:** The system **SHALL** redirect the user to the organization dashboard after successful invite acceptance.

## Context

Invite acceptance is processed by the `acceptInvite` Cloud Function. The function validates the invite token, checks expiration, creates the member document in Firestore, and fires the `MemberJoined` domain event. The invite link works for both authenticated and unauthenticated users, guiding them through the appropriate flow (login or signup) before joining.

## Classification

| Field           | Value             |
| --------------- | ----------------- |
| Safety Class    | A                 |
| Category        | Functional        |
| Bounded Context | Identity & Access |

## Detailed Specification

### Inputs

- Invite token (from URL)
- User authentication (existing account or new signup)

### Processing

1. Validate invite token exists in Firestore.
2. Check invite has not expired (within 7-day window).
3. Check invite has not already been accepted.
4. If user is not authenticated, redirect to login/signup with return URL.
5. Create member document with pre-assigned department and role.
6. Mark invite as accepted.
7. Fire `MemberJoined` domain event.
8. Create audit log entry.

### Outputs

- New member document in Firestore
- Invite marked as accepted
- Redirect to organization dashboard

### Error Handling

- Invalid token: display "This invite link is not valid"
- Expired invite: display "This invite has expired. Please request a new invitation."
- Already accepted: display "This invite has already been used"
- User already a member: display appropriate message

## Acceptance Criteria

1. **Given** an existing user with a valid invite link, **when** they click the link and log in, **then** they are added to the organization with the assigned department and role.
2. **Given** a new user with a valid invite link, **when** they click the link and sign up, **then** they are added to the organization after signup.
3. **Given** an expired invite link, **when** a user clicks it, **then** a clear error message about expiration is displayed.
4. **Given** an already-accepted invite link, **when** a user clicks it, **then** they are informed the invite has already been used.
5. **Given** successful invite acceptance, **when** the user joins, **then** they appear in the organization member list.

## Traceability

| Direction | Link Type    | Target ID | Title               |
| --------- | ------------ | --------- | ------------------- |
| Up        | derives-from | PRS-003    | Invitation workflow |

## Source

**STORY-107 — Accept Invite**

> **As a** person who received an invite **I want** to accept the invite and join the organization **So that** I can access the team's QMS documents.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
