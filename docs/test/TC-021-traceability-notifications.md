---
id: TC-021
title: 'Traceability & Notifications Verification'
status: draft
links:
  - type: verified-by
    target: SRS-308
---

# Traceability & Notifications Verification

## 1. Purpose

Verifies the traceability service, traceability API client, notifications repository, notification service, and email notification templates. These tests validate cross-domain concerns that span traceability (document linking) and the notification subsystem.

## 2. Test Type

| Field         | Value                               |
| ------------- | ----------------------------------- |
| Document Type | Test Report (TEST)                  |
| Test Level    | Unit                                |
| Test Method   | Automated (Vitest)                  |
| Environment   | Local (vitest), CI (GitHub Actions) |

## 3. Scope

### In Scope

- Traceability service: link matrix computation, coverage calculation
- Traceability API client
- Notifications repository: CRUD for in-app notifications
- Notification service: orchestration of notification delivery
- Email notification templates rendering
- Members repository: member lookup for notifications

### Out of Scope

- Frontend traceability page (see TC-008)
- E2E traceability journey (see TC-008)
- Audit log notifications (see TC-009)

## 4. Test Coverage

| Test File                                                                    | Package              | Verifies                                 |
| ---------------------------------------------------------------------------- | -------------------- | ---------------------------------------- |
| `apps/services/src/modules/documents/TraceabilityService.test.ts`            | @pactosigna/services | Traceability matrix and coverage         |
| `apps/web/src/shared/services/api/__tests__/traceability-api.test.ts`        | @pactosigna/web      | Traceability REST API client             |
| `packages/data/src/repositories/NotificationsRepository.test.ts`             | @pactosigna/data     | Notification CRUD operations             |
| `packages/data/src/services/__tests__/NotificationService.test.ts`           | @pactosigna/data     | Notification delivery orchestration      |
| `packages/data/src/services/email-templates/__tests__/notifications.test.ts` | @pactosigna/data     | Email template rendering                 |
| `packages/data/src/repositories/MembersRepository.test.ts`                   | @pactosigna/data     | Member lookup for notification targeting |

## 5. Pass Criteria

- Traceability service computes link matrix between document types (SRS-308)
- Traceability API client fetches matrix data correctly (SRS-308)
- Notifications are created and retrieved per user (cross-cutting)
- Email templates render with correct variables and formatting (cross-cutting)
- Member repository supports lookups needed for notification targeting (cross-cutting)

## 6. Test Results Summary

| Metric      | Value   |
| ----------- | ------- |
| Total Cases | Pending |
| Passed      | Pending |
| Failed      | Pending |
| Pass Rate   | Pending |

## 7. Traceability

| Direction | Link Type | Target ID | Title                    |
| --------- | --------- | --------- | ------------------------ |
| Up        | verifies  | SRS-308   | Traceability Matrix View |
