---
id: TC-003
title: 'Identity & Access — Team Management & E2E Tests'
status: draft
links:
  - type: verified-by
    target: SRS-106
  - type: verified-by
    target: SRS-107
  - type: verified-by
    target: SRS-108
---

# Identity & Access — Team Management & E2E Tests

## 1. Purpose

Verifies team management capabilities (invites, member management) at both the unit and E2E levels. Includes the user onboarding and team collaboration E2E journeys that exercise the full Identity & Access flow end-to-end.

## 2. Test Type

| Field         | Value                                        |
| ------------- | -------------------------------------------- |
| Document Type | Test Report (TEST)                           |
| Test Level    | Unit / System (E2E)                          |
| Test Method   | Automated (Vitest + Playwright BDD)          |
| Environment   | Local (vitest), Firebase Emulators (E2E), CI |

## 3. Scope

### In Scope

- Invite creation and acceptance backend logic
- Member management service (role changes, removal)
- Identity API client tests (frontend)
- E2E: Full user onboarding journey (signup → org creation → dashboard)
- E2E: Team collaboration journey (invite → accept → team view)

### Out of Scope

- Organization CRUD unit tests (see TC-002)
- Auth middleware tests (see TC-001)

## 4. Test Coverage

| Test File                                                         | Package              | Verifies                                |
| ----------------------------------------------------------------- | -------------------- | --------------------------------------- |
| `apps/services/src/modules/identity/InvitesService.test.ts`       | @pactosigna/services | Invite creation, validation, acceptance |
| `apps/services/src/modules/identity/MembersService.test.ts`       | @pactosigna/services | Member listing, role update, removal    |
| `apps/web/src/shared/services/api/__tests__/identity-api.test.ts` | @pactosigna/web      | Identity REST API client functions      |
| `e2e/features/journeys/01-user-onboarding.feature`                | e2e                  | Full signup and onboarding flow         |
| `e2e/features/journeys/04-team-collaboration.feature`             | e2e                  | Invite members and team management flow |

## 5. Pass Criteria

- Invites are created with correct org and role data (SRS-106)
- Invite acceptance adds user as member to the organization (SRS-107)
- Member roles can be updated and members can be removed (SRS-108)
- E2E: User can sign up, create org, and reach dashboard (SRS-101, SRS-104)
- E2E: User can invite another user and they appear in team list (SRS-106, SRS-107)

## 6. Test Results Summary

| Metric      | Value   |
| ----------- | ------- |
| Total Cases | Pending |
| Passed      | Pending |
| Failed      | Pending |
| Pass Rate   | Pending |

## 7. Traceability

| Direction | Link Type | Target ID | Title               |
| --------- | --------- | --------- | ------------------- |
| Up        | verifies  | SRS-106   | Invite Team Members |
| Up        | verifies  | SRS-107   | Accept Invite       |
| Up        | verifies  | SRS-108   | Manage Members      |
