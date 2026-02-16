---
id: SRS-102
title: 'User Login'
status: draft
links:
  - type: derives-from
    target: PRS-001
---

# User Login

## Requirement

> **SRS-102.1:** The system **SHALL** provide a login form that collects email address and password, and authenticate the user via Firebase Auth.
>
> **SRS-102.2:** The system **SHALL** prevent login for users whose email address has not been verified.
>
> **SRS-102.3:** The system **SHALL** persist the user session across browser refreshes using Firebase Auth session persistence.
>
> **SRS-102.4:** The system **SHALL** redirect authenticated users to the organization dashboard if they belong to one organization, display an organization selector if they belong to multiple, or redirect to "Create/Join Organization" if they belong to none.
>
> **SRS-102.5:** The system **SHALL** provide a "Forgot password" link that initiates the password reset flow.

## Context

This requirement covers the authentication flow for returning users. Firebase Auth `signInWithEmailAndPassword` is used for credential verification. The system queries organization memberships upon login to determine the correct redirect destination. Session persistence ensures users remain logged in across page reloads.

## Classification

| Field           | Value             |
| --------------- | ----------------- |
| Safety Class    | A                 |
| Category        | Security          |
| Bounded Context | Identity & Access |

## Detailed Specification

### Inputs

- Email address
- Password

### Processing

1. Authenticate via Firebase Auth `signInWithEmailAndPassword`.
2. Check email verification status.
3. Query user's organization memberships.
4. Determine redirect destination based on membership count.

### Outputs

- Authenticated session (Firebase Auth token)
- Redirect to appropriate screen based on membership state

### Error Handling

- Invalid credentials: display "Invalid email or password"
- Unverified email: display "Please verify your email before logging in"
- Network errors: display generic retry message

## Acceptance Criteria

1. **Given** a registered user with a verified email, **when** they enter valid credentials, **then** they are authenticated and redirected to their organization dashboard.
2. **Given** a user with an unverified email, **when** they attempt to log in, **then** login is rejected with a verification prompt.
3. **Given** an authenticated user who refreshes the browser, **when** the page reloads, **then** the session persists and they remain logged in.
4. **Given** a user belonging to multiple organizations, **when** they log in, **then** an organization selector is displayed.
5. **Given** a user belonging to no organizations, **when** they log in, **then** they are redirected to "Create Organization" or "Join Organization".

## Traceability

| Direction | Link Type    | Target ID | Title                  |
| --------- | ------------ | --------- | ---------------------- |
| Up        | derives-from | PRS-001    | Organization lifecycle |

## Source

**STORY-102 — User Login**

> **As a** registered user **I want** to log in with my email and password **So that** I can access my organization.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
