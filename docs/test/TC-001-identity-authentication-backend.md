---
id: TC-001
title: 'Identity & Access — Authentication Backend Tests'
status: draft
links:
  - type: verified-by
    target: SRS-101
  - type: verified-by
    target: SRS-102
  - type: verified-by
    target: SRS-103
---

# Identity & Access — Authentication Backend Tests

## 1. Purpose

Verifies backend authentication logic including user signup, login flows, password reset, and the Firebase Auth trigger that provisions new user profiles. These tests ensure the Identity bounded context's server-side authentication requirements are met.

## 2. Test Type

| Field         | Value                               |
| ------------- | ----------------------------------- |
| Document Type | Test Report (TEST)                  |
| Test Level    | Unit                                |
| Test Method   | Automated (Vitest)                  |
| Environment   | Local (vitest), CI (GitHub Actions) |

## 3. Scope

### In Scope

- User signup auth trigger (onBeforeUserCreated)
- User profile provisioning on first login
- Identity controller request validation
- Identity service orchestration logic
- Auth plugin middleware (token verification, role extraction)

### Out of Scope

- Frontend login/signup UI (see TC-002)
- E2E signup flow (see TC-003)
- OAuth provider integration (Firebase Auth manages externally)

## 4. Test Coverage

| Test File                                                       | Package               | Verifies                                              |
| --------------------------------------------------------------- | --------------------- | ----------------------------------------------------- |
| `apps/services/src/modules/identity/identity.test.ts`           | @pactosigna/services  | Identity module integration                           |
| `apps/services/src/modules/identity/IdentityController.test.ts` | @pactosigna/services  | HTTP request/response handling for identity endpoints |
| `apps/services/src/plugins/auth.test.ts`                        | @pactosigna/services  | Auth middleware: token validation, role extraction    |
| `apps/functions/src/auth/onBeforeUserCreated.test.ts`           | @pactosigna/functions | Firebase Auth trigger on new user creation            |
| `apps/functions/src/auth/profile.test.ts`                       | @pactosigna/functions | Profile provisioning for newly created users          |

## 5. Pass Criteria

- Auth trigger creates user profile record on first signup (SRS-101)
- Auth middleware correctly extracts and validates Firebase ID tokens (SRS-102)
- Identity controller rejects malformed requests with proper error codes (SRS-102)
- Password reset flow delegates to Firebase Auth correctly (SRS-103)

## 6. Test Results Summary

| Metric      | Value   |
| ----------- | ------- |
| Total Cases | Pending |
| Passed      | Pending |
| Failed      | Pending |
| Pass Rate   | Pending |

## 7. Traceability

| Direction | Link Type | Target ID | Title          |
| --------- | --------- | --------- | -------------- |
| Up        | verifies  | SRS-101   | User Signup    |
| Up        | verifies  | SRS-102   | User Login     |
| Up        | verifies  | SRS-103   | Password Reset |
