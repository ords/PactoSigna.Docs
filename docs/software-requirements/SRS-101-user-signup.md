---
id: SRS-101
title: 'User Signup'
status: draft
links:
  - type: derives-from
    target: PRS-001
---

# User Signup

## Requirement

> **SRS-101.1:** The system **SHALL** provide a signup form that collects email address, password, confirm password, and display name.
>
> **SRS-101.2:** The system **SHALL** enforce password complexity requiring a minimum of 8 characters with at least 1 uppercase letter, 1 lowercase letter, and 1 number.
>
> **SRS-101.3:** The system **SHALL** send an email verification upon successful signup and prevent access to the application until the email address is verified.
>
> **SRS-101.4:** The system **SHALL** display specific error messages for duplicate email, weak password, and mismatched password conditions.
>
> **SRS-101.5:** The system **SHALL** redirect the user to a "Create Organization" or "Join Organization" choice upon successful signup and email verification.

## Context

This requirement covers the initial user registration flow for PactoSigna. User accounts are created via Firebase Auth (`createUserWithEmailAndPassword`), and a minimal user profile is stored in Firestore only after email verification. All account creation is processed through a Cloud Function to ensure audit logging per Part 11 requirements.

## Classification

| Field           | Value             |
| --------------- | ----------------- |
| Safety Class    | A                 |
| Category        | Functional        |
| Bounded Context | Identity & Access |

## Detailed Specification

### Inputs

- Email address (valid email format)
- Password (meeting complexity requirements)
- Confirm password (must match password)
- Display name (non-empty string)

### Processing

1. Validate all input fields client-side and server-side.
2. Create Firebase Auth account with `createUserWithEmailAndPassword`.
3. Send email verification via `sendEmailVerification`.
4. Store minimal user profile in Firestore after email verification (via Cloud Function).
5. Create audit log entry for account creation.

### Outputs

- New Firebase Auth user account
- Email verification sent to the provided address
- Firestore user profile document (post-verification)
- Redirect to organization selection screen

### Error Handling

- Duplicate email: display "An account with this email already exists"
- Weak password: display password requirements
- Mismatched passwords: display "Passwords do not match"
- Network errors: display generic retry message

## Acceptance Criteria

1. **Given** a new user on the signup page, **when** they submit valid credentials, **then** a Firebase Auth account is created and a verification email is sent.
2. **Given** an unverified user, **when** they attempt to access the application, **then** access is denied with a prompt to verify their email.
3. **Given** a user submitting a password with fewer than 8 characters, **when** form validation runs, **then** an error message indicating the password requirements is displayed.
4. **Given** an email already registered, **when** a new signup is attempted with that email, **then** a duplicate email error is shown.
5. **Given** a verified user, **when** they complete signup, **then** they are redirected to the "Create Organization" or "Join Organization" screen.

## Traceability

| Direction | Link Type    | Target ID | Title                  |
| --------- | ------------ | --------- | ---------------------- |
| Up        | derives-from | PRS-001    | Organization lifecycle |

## Source

**STORY-101 — User Signup**

> **As a** new user **I want** to create an account with email and password **So that** I can access PactoSigna.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
