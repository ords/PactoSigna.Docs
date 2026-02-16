---
id: SRS-103
title: 'Password Reset'
status: draft
links:
  - type: derives-from
    target: PRS-001
---

# Password Reset

## Requirement

> **SRS-103.1:** The system **SHALL** provide a "Forgot password" link on the login page that navigates to a password reset form.
>
> **SRS-103.2:** The system **SHALL** send a password reset email via Firebase Auth when a valid email address is submitted.
>
> **SRS-103.3:** The system **SHALL** enforce the same password complexity requirements (minimum 8 characters, 1 uppercase, 1 lowercase, 1 number) for the new password.
>
> **SRS-103.4:** The system **SHALL** redirect the user to the login page with a success message after a successful password reset.

## Context

Password reset is handled via Firebase Auth `sendPasswordResetEmail`. The reset link in the email directs users to a Firebase-hosted or custom reset page. This flow is essential for account recovery and must enforce the same password complexity rules as signup.

## Classification

| Field           | Value             |
| --------------- | ----------------- |
| Safety Class    | A                 |
| Category        | Security          |
| Bounded Context | Identity & Access |

## Detailed Specification

### Inputs

- Email address (for reset request)
- New password (on reset page, meeting complexity requirements)

### Processing

1. User submits email on "Forgot password" form.
2. Firebase Auth sends password reset email with secure link.
3. User clicks link and enters new password.
4. Firebase Auth validates and updates the password.

### Outputs

- Password reset email sent to the provided address
- Updated password in Firebase Auth
- Redirect to login page with success message

### Error Handling

- Email not found: for security, still display "If the email exists, a reset link has been sent"
- Invalid or expired reset link: display "This link has expired. Please request a new reset."
- New password does not meet complexity requirements: display requirements

## Acceptance Criteria

1. **Given** a user on the login page, **when** they click "Forgot password", **then** a password reset form is displayed.
2. **Given** a valid email submitted on the reset form, **when** the request is processed, **then** a password reset email is sent.
3. **Given** a user clicks the reset link, **when** they enter a new password meeting complexity requirements, **then** the password is updated and they are redirected to login with a success message.
4. **Given** a user enters a weak new password, **when** they submit the reset form, **then** an error indicating the complexity requirements is shown.

## Traceability

| Direction | Link Type    | Target ID | Title                  |
| --------- | ------------ | --------- | ---------------------- |
| Up        | derives-from | PRS-001    | Organization lifecycle |

## Source

**STORY-103 — Password Reset**

> **As a** user who forgot my password **I want** to reset my password via email **So that** I can regain access to my account.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
