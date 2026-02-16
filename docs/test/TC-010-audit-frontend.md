---
id: TC-010
title: 'Audit Infrastructure — Frontend Tests'
status: draft
links:
  - type: verified-by
    target: SRS-402
  - type: verified-by
    target: SRS-403
---

# Audit Infrastructure — Frontend Tests

## 1. Purpose

Verifies the frontend audit log viewer page and API client. These tests ensure users can browse and inspect audit log entries through the web interface.

## 2. Test Type

| Field         | Value                               |
| ------------- | ----------------------------------- |
| Document Type | Test Report (TEST)                  |
| Test Level    | Unit                                |
| Test Method   | Automated (Vitest)                  |
| Environment   | Local (vitest), CI (GitHub Actions) |

## 3. Scope

### In Scope

- Audit log page rendering (list view with filtering)
- Audit API client functions
- Audit entry detail display

### Out of Scope

- Audit log creation (see TC-009)
- Audit retention enforcement (see TC-009)

## 4. Test Coverage

| Test File                                                           | Package         | Verifies                               |
| ------------------------------------------------------------------- | --------------- | -------------------------------------- |
| `apps/web/src/shared/services/api/__tests__/audit-api.test.ts`      | @pactosigna/web | Audit REST API client functions        |
| `apps/web/src/features/audit/pages/__tests__/AuditLogPage.test.tsx` | @pactosigna/web | Audit log page rendering and filtering |

## 5. Pass Criteria

- Audit log page renders list of entries with timestamps and actions (SRS-402)
- Audit entries can be filtered by action type and date range (SRS-402)
- Audit entry detail shows full payload including user, resource, and metadata (SRS-403)
- API client correctly calls audit endpoints with proper parameters (SRS-402)

## 6. Test Results Summary

| Metric      | Value   |
| ----------- | ------- |
| Total Cases | Pending |
| Passed      | Pending |
| Failed      | Pending |
| Pass Rate   | Pending |

## 7. Traceability

| Direction | Link Type | Target ID | Title              |
| --------- | --------- | --------- | ------------------ |
| Up        | verifies  | SRS-402   | Audit Log Viewer   |
| Up        | verifies  | SRS-403   | Audit Entry Detail |
