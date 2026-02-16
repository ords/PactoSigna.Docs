---
id: SRS-109
title: 'View My Profile'
status: draft
links:
  - type: derives-from
    target: PRS-002
---

# View My Profile

## Requirement

> **SRS-109.1:** The system **SHALL** display a profile page showing the member's display name, email, department, role, and joined date.
>
> **SRS-109.2:** The system **SHALL** allow members to update their display name via the `updateProfile` Cloud Function.
>
> **SRS-109.3:** The system **SHALL** allow members to change their password through Firebase Auth.
>
> **SRS-109.4:** The system **SHALL** prevent members from changing their email address directly (Firebase Auth limitation).

## Context

The profile page provides self-service identity management for members. Display name updates are processed through a Cloud Function for audit logging. Department changes for non-admin users may require admin approval (future enhancement). Email changes are restricted due to Firebase Auth constraints or would require re-verification.

## Classification

| Field           | Value             |
| --------------- | ----------------- |
| Safety Class    | A                 |
| Category        | Functional        |
| Bounded Context | Identity & Access |

## Detailed Specification

### Inputs

- New display name (for name update)
- Current password and new password (for password change)

### Processing

1. Display current profile information from Firestore member document.
2. For name updates: call `updateProfile` Cloud Function.
3. For password changes: use Firebase Auth password update flow.
4. Create audit log entry for profile updates.

### Outputs

- Updated display name in Firestore
- Updated password in Firebase Auth
- Audit log entry for changes

### Error Handling

- Invalid display name (empty or exceeds length): display validation error
- Incorrect current password for password change: display authentication error
- New password does not meet complexity requirements: display requirements

## Acceptance Criteria

1. **Given** an authenticated member, **when** they navigate to the profile page, **then** their display name, email, department, role, and joined date are shown.
2. **Given** a member updating their display name, **when** they submit a valid name, **then** the name is updated and an audit log entry is created.
3. **Given** a member changing their password, **when** they provide a valid current password and a new password meeting complexity requirements, **then** the password is updated.
4. **Given** a member on the profile page, **when** they attempt to change their email, **then** the email field is not editable.

## Traceability

| Direction | Link Type    | Target ID | Title                  |
| --------- | ------------ | --------- | ---------------------- |
| Up        | derives-from | PRS-002    | Member role management |

## Source

**STORY-109 — View My Profile**

> **As a** member **I want** to view and update my profile **So that** my information is correct.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
