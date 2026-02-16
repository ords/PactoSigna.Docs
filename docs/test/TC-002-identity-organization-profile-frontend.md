---
id: TC-002
title: 'Identity & Access — Organization & Profile Frontend Tests'
status: draft
links:
  - type: verified-by
    target: SRS-104
  - type: verified-by
    target: SRS-105
  - type: verified-by
    target: SRS-109
---

# Identity & Access — Organization & Profile Frontend Tests

## 1. Purpose

Verifies frontend components and hooks for organization management, profile display, dashboard rendering, and organization settings. These tests cover the React UI layer for the Identity & Access bounded context.

## 2. Test Type

| Field         | Value                               |
| ------------- | ----------------------------------- |
| Document Type | Test Report (TEST)                  |
| Test Level    | Unit                                |
| Test Method   | Automated (Vitest)                  |
| Environment   | Local (vitest), CI (GitHub Actions) |

## 3. Scope

### In Scope

- Organization creation and switching UI
- Organization context provider behavior
- Organization gate (access control rendering)
- Dashboard statistics and skeleton loading states
- Getting started checklist component
- Profile page display and settings hooks
- Organization settings hooks

### Out of Scope

- Backend organization CRUD (see TC-001)
- E2E user onboarding (see TC-003)
- Team invitation flows (see TC-003)

## 4. Test Coverage

| Test File                                                                                  | Package              | Verifies                                       |
| ------------------------------------------------------------------------------------------ | -------------------- | ---------------------------------------------- |
| `apps/services/src/modules/identity/OrganizationsService.test.ts`                          | @pactosigna/services | Organization CRUD service logic                |
| `apps/services/src/modules/identity/ProfileService.test.ts`                                | @pactosigna/services | Profile read/update service logic              |
| `apps/web/src/features/organization/context/__tests__/OrganizationContext.test.tsx`        | @pactosigna/web      | Organization context provider state management |
| `apps/web/src/features/organization/components/__tests__/OrganizationGate.test.tsx`        | @pactosigna/web      | Access gating based on org membership          |
| `apps/web/src/features/organization/components/__tests__/OrgSwitcher.test.tsx`             | @pactosigna/web      | Organization switcher dropdown                 |
| `apps/web/src/features/organization/components/__tests__/DashboardStats.test.tsx`          | @pactosigna/web      | Dashboard statistics display                   |
| `apps/web/src/features/organization/components/__tests__/DashboardSkeleton.test.tsx`       | @pactosigna/web      | Dashboard loading skeleton                     |
| `apps/web/src/features/organization/components/__tests__/GettingStartedChecklist.test.tsx` | @pactosigna/web      | Onboarding checklist component                 |
| `apps/web/src/features/organization/components/__tests__/RecentActivity.test.tsx`          | @pactosigna/web      | Recent activity feed                           |
| `apps/web/src/features/organization/components/__tests__/SampleReposCard.test.tsx`         | @pactosigna/web      | Sample repositories card                       |
| `apps/web/src/features/organization/hooks/__tests__/useDashboardData.test.tsx`             | @pactosigna/web      | Dashboard data fetching hook                   |
| `apps/web/src/features/settings/pages/__tests__/ProfilePage.test.tsx`                      | @pactosigna/web      | Profile page rendering                         |
| `apps/web/src/features/settings/hooks/__tests__/useOrganizationSettings.test.ts`           | @pactosigna/web      | Organization settings hook                     |

## 5. Pass Criteria

- Organization context correctly resolves current org and loading states (SRS-104)
- Organization gate blocks access when user is not a member (SRS-104)
- Org switcher renders all user organizations and handles selection (SRS-104)
- Organization settings hook reads and updates settings (SRS-105)
- Profile page displays user information correctly (SRS-109)

## 6. Test Results Summary

| Metric      | Value   |
| ----------- | ------- |
| Total Cases | Pending |
| Passed      | Pending |
| Failed      | Pending |
| Pass Rate   | Pending |

## 7. Traceability

| Direction | Link Type | Target ID | Title                 |
| --------- | --------- | --------- | --------------------- |
| Up        | verifies  | SRS-104   | Create Organization   |
| Up        | verifies  | SRS-105   | Organization Settings |
| Up        | verifies  | SRS-109   | View My Profile       |
