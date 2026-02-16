---
id: TC-012
title: 'Release Management — Frontend Tests'
status: draft
links:
  - type: verified-by
    target: SRS-501
  - type: verified-by
    target: SRS-504
  - type: verified-by
    target: SRS-505
---

# Release Management — Frontend Tests

## 1. Purpose

Verifies frontend pages, components, and hooks for release creation, listing, detail viewing, and change control management. These tests cover the React UI layer for the Release Management bounded context.

## 2. Test Type

| Field         | Value                               |
| ------------- | ----------------------------------- |
| Document Type | Test Report (TEST)                  |
| Test Level    | Unit                                |
| Test Method   | Automated (Vitest)                  |
| Environment   | Local (vitest), CI (GitHub Actions) |

## 3. Scope

### In Scope

- Create release page form
- Releases list page
- Release detail page
- Release sidebar component
- Release detail hook
- Change controls API client and create page
- Releases API client

### Out of Scope

- Backend release lifecycle (see TC-011)
- E2E release workflows (see TC-013)
- Signature UI components (see TC-015)

## 4. Test Coverage

| Test File                                                                                | Package         | Verifies                                |
| ---------------------------------------------------------------------------------------- | --------------- | --------------------------------------- |
| `apps/web/src/shared/services/api/__tests__/releases-api.test.ts`                        | @pactosigna/web | Releases REST API client                |
| `apps/web/src/shared/services/api/__tests__/change-controls-api.test.ts`                 | @pactosigna/web | Change controls REST API client         |
| `apps/web/src/features/releases/pages/__tests__/CreateReleasePage.test.tsx`              | @pactosigna/web | Create release form and submission      |
| `apps/web/src/features/releases/pages/__tests__/ReleasesPage.test.tsx`                   | @pactosigna/web | Releases list page rendering            |
| `apps/web/src/features/releases/pages/__tests__/ReleaseDetailPage.test.tsx`              | @pactosigna/web | Release detail page display             |
| `apps/web/src/features/releases/hooks/__tests__/useReleaseDetail.test.ts`                | @pactosigna/web | Release detail data fetching hook       |
| `apps/web/src/features/releases/components/__tests__/ReleaseSidebar.test.tsx`            | @pactosigna/web | Release sidebar with status and actions |
| `apps/web/src/features/change-controls/pages/__tests__/CreateChangeControlPage.test.tsx` | @pactosigna/web | Change control creation page            |

## 5. Pass Criteria

- Create release page validates inputs and submits correctly (SRS-501)
- Releases list page renders releases with status badges (SRS-505)
- Release detail page shows documents, signatures, and status (SRS-504)
- Release sidebar shows contextual actions based on release state (SRS-504)
- Release detail hook fetches and caches release data (SRS-504)

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
| Up        | verifies  | SRS-501   | Create Release Bundle |
| Up        | verifies  | SRS-504   | View Release Details  |
| Up        | verifies  | SRS-505   | List Releases         |
