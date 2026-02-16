---
id: SRS-605
title: 'Re-authentication for Signing'
status: draft
links:
  - type: derives-from
    target: PRS-015
---

# Re-authentication for Signing

## Requirement

> **SRS-605.1:** The system **SHALL** require the user to re-enter their password before any signature is accepted, using Firebase Auth `reauthenticateWithCredential`.
>
> **SRS-605.2:** The system **SHALL** reject the signature if re-authentication fails and limit attempts to a maximum of 3 before locking out the signing dialog.
>
> **SRS-605.3:** The system **SHALL** create an audit log entry for failed re-authentication attempts.

## Context

Re-authentication is a core Part 11 compliance requirement ensuring the signer is the account holder. `reauthenticateWithCredential` from Firebase Auth validates the password against the current session. Failed attempts are logged for security audit purposes. The 3-attempt lockout prevents brute-force attacks.

## Classification

| Field           | Value              |
| --------------- | ------------------ |
| Safety Class    | B                  |
| Category        | Security           |
| Bounded Context | Release Management |

## Detailed Specification

### Inputs

- Password (re-entered by the user)

### Processing

1. Prompt user for password in signing dialog.
2. Call `reauthenticateWithCredential` with email and password.
3. On success: proceed with signature capture.
4. On failure: increment attempt counter, log failed attempt.
5. After 3 failed attempts: lock signing dialog.

### Outputs

- Successful re-authentication (proceed to signature)
- Failed re-authentication (attempt logged)
- Lockout after 3 failures

### Error Handling

- Incorrect password: display "Incorrect password. X attempts remaining."
- 3 failed attempts: lock dialog with "Too many failed attempts. Please try again later."
- Network error: display "Authentication service unavailable"

## Acceptance Criteria

1. **Given** a user entering the correct password, **when** re-authenticating, **then** the signature flow proceeds.
2. **Given** a user entering an incorrect password, **when** re-authenticating, **then** the attempt is rejected and an audit log entry is created.
3. **Given** 3 consecutive failed attempts, **when** the third failure occurs, **then** the signing dialog is locked.
4. **Given** a successful re-authentication, **when** examining the flow, **then** the signature is bound to the authenticated user.

## Traceability

| Direction | Link Type    | Target ID | Title                    |
| --------- | ------------ | --------- | ------------------------ |
| Up        | derives-from | PRS-015    | Signing request workflow |

## Source

**STORY-605 — Re-authentication for Signing**

> **As a** User **I want** to re-enter my password before signing **So that** the signature is verified as mine.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
