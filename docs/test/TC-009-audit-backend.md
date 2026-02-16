---
id: TC-009
title: 'Audit Infrastructure — Backend Tests'
status: draft
links:
  - type: verified-by
    target: SRS-401
  - type: verified-by
    target: SRS-404
  - type: verified-by
    target: SRS-405
---

# Audit Infrastructure — Backend Tests

## 1. Purpose

Verifies the backend audit log service and Cloud Functions audit utilities. These tests ensure that all mutations produce audit log entries as required by Part 11 compliance, and that the audit actions enum is complete.

## 2. Test Type

| Field         | Value                               |
| ------------- | ----------------------------------- |
| Document Type | Test Report (TEST)                  |
| Test Level    | Unit                                |
| Test Method   | Automated (Vitest)                  |
| Environment   | Local (vitest), CI (GitHub Actions) |

## 3. Scope

### In Scope

- Audit service: log entry creation, querying, filtering
- Audit actions enum completeness
- Cloud Functions audit utility (shared audit helper)
- Audit log retention policy enforcement

### Out of Scope

- Audit log viewer UI (see TC-010)
- Cross-cutting audit assertions in other modules (verified implicitly by service tests)

## 4. Test Coverage

| Test File                                              | Package               | Verifies                                |
| ------------------------------------------------------ | --------------------- | --------------------------------------- |
| `apps/services/src/modules/audit/AuditService.test.ts` | @pactosigna/services  | Audit log creation, listing, filtering  |
| `apps/functions/src/shared/audit.test.ts`              | @pactosigna/functions | Shared audit helper for Cloud Functions |

## 5. Pass Criteria

- Audit service creates log entries with userId, action, resource, and timestamp (SRS-401)
- Audit log entries are queryable by action type and date range (SRS-401)
- Audit retention policy marks entries beyond retention period (SRS-404)
- All audit action types are defined in the enum and validated (SRS-405)

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
| Up        | verifies  | SRS-401   | Audit Log Service   |
| Up        | verifies  | SRS-404   | Audit Log Retention |
| Up        | verifies  | SRS-405   | Audit Actions Enum  |
