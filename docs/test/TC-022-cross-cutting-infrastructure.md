---
id: TC-022
title: 'Cross-Cutting Infrastructure Tests'
status: draft
links:
  - type: verified-by
    target: SRS-102
  - type: verified-by
    target: SRS-401
---

# Cross-Cutting Infrastructure Tests

## 1. Purpose

Verifies cross-cutting infrastructure: the base API client, error handling plugin, Firestore index assertions, shared utilities, and layout components. These foundational tests ensure the platform substrate is reliable for all bounded contexts.

## 2. Test Type

| Field         | Value                               |
| ------------- | ----------------------------------- |
| Document Type | Test Report (TEST)                  |
| Test Level    | Unit                                |
| Test Method   | Automated (Vitest)                  |
| Environment   | Local (vitest), CI (GitHub Actions) |

## 3. Scope

### In Scope

- Base API client (axios configuration, interceptors, error mapping)
- API module barrel export validation
- Error handler plugin (Fastify error serialization)
- Error notification hook (frontend toast on API errors)
- Dashboard layout component
- Firestore composite index assertions
- Shared package utilities (stripUndefined, general utils)
- Shared package barrel export
- Shared Zod schemas

### Out of Scope

- Domain-specific services (covered by domain TEST docs)
- E2E infrastructure (covered by TP-001)

## 4. Test Coverage

| Test File                                                          | Package              | Verifies                               |
| ------------------------------------------------------------------ | -------------------- | -------------------------------------- |
| `apps/web/src/shared/services/api/__tests__/base.test.ts`          | @pactosigna/web      | Base API client setup and interceptors |
| `apps/web/src/shared/services/api/__tests__/index.test.ts`         | @pactosigna/web      | API module barrel exports              |
| `apps/web/src/shared/hooks/__tests__/useErrorNotification.test.ts` | @pactosigna/web      | Error notification toast hook          |
| `apps/web/src/shared/layouts/__tests__/DashboardLayout.test.tsx`   | @pactosigna/web      | Dashboard layout rendering             |
| `apps/web/src/shared/utils/shouldRetryQuery.test.ts`               | @pactosigna/web      | React Query retry policy               |
| `apps/services/src/plugins/error-handler.test.ts`                  | @pactosigna/services | Fastify error handler plugin           |
| `packages/data/src/utils/stripUndefined.test.ts`                   | @pactosigna/data     | Utility: strip undefined fields        |
| `packages/data/src/repositories/firestore-indexes.test.ts`         | @pactosigna/data     | Firestore composite index assertions   |
| `packages/shared/src/index.test.ts`                                | @pactosigna/shared   | Shared package barrel export           |
| `packages/shared/src/utils/utils.test.ts`                          | @pactosigna/shared   | Shared utility functions               |
| `packages/shared/src/schemas/schemas.test.ts`                      | @pactosigna/shared   | Shared Zod schema validation           |
| `apps/web/src/test/placeholder.test.tsx`                           | @pactosigna/web      | Test infrastructure placeholder        |

## 5. Pass Criteria

- Base API client adds auth headers and maps errors correctly (SRS-102)
- Error handler returns RFC 7807 problem details for all error types
- Firestore indexes match all queries in the codebase (SRS-401, cross-cutting)
- Shared schemas validate domain entities correctly
- Dashboard layout renders navigation and content areas

## 6. Test Results Summary

| Metric      | Value   |
| ----------- | ------- |
| Total Cases | Pending |
| Passed      | Pending |
| Failed      | Pending |
| Pass Rate   | Pending |

## 7. Traceability

| Direction | Link Type | Target ID | Title             |
| --------- | --------- | --------- | ----------------- |
| Up        | verifies  | SRS-102   | User Login        |
| Up        | verifies  | SRS-401   | Audit Log Service |
