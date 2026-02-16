---
id: TC-016
title: 'Training Management — Backend & Worker Tests'
status: draft
links:
  - type: verified-by
    target: SRS-701
  - type: verified-by
    target: SRS-705
  - type: verified-by
    target: SRS-706
---

# Training Management — Backend & Worker Tests

## 1. Purpose

Verifies the training service, enrichment logic, assignment worker, and overdue notification worker. These tests ensure server-side training compliance features work correctly, including auto-assignment and completion scoring (Part 11 — server-side only per ADR-018).

## 2. Test Type

| Field         | Value                               |
| ------------- | ----------------------------------- |
| Document Type | Test Report (TEST)                  |
| Test Level    | Unit                                |
| Test Method   | Automated (Vitest)                  |
| Environment   | Local (vitest), CI (GitHub Actions) |

## 3. Scope

### In Scope

- Training service: task creation, completion, grading
- Training enrichment: adding user/document context to tasks
- Training assignment worker (Cloud Tasks): auto-assign on release publish
- Training overdue worker (Cloud Scheduler): daily overdue checks
- Server-side quiz scoring (ADR-018: never trust client data)

### Out of Scope

- Frontend training UI (see TC-017)
- E2E training flows (see TC-018)

## 4. Test Coverage

| Test File                                                       | Package               | Verifies                                  |
| --------------------------------------------------------------- | --------------------- | ----------------------------------------- |
| `apps/services/src/modules/training/TrainingService.test.ts`    | @pactosigna/services  | Training task CRUD and completion logic   |
| `apps/services/src/modules/training/trainingEnrichment.test.ts` | @pactosigna/services  | Training task enrichment with context     |
| `apps/functions/src/trainingAssignmentWorker.test.ts`           | @pactosigna/functions | Auto-assignment worker on release publish |
| `apps/functions/src/trainingOverdueWorker.test.ts`              | @pactosigna/functions | Overdue training notification CRON        |

## 5. Pass Criteria

- Training tasks are auto-assigned when change control effectiveness triggers (SRS-701)
- Quiz-based training is scored server-side with passing threshold (SRS-705)
- Acknowledge-based training marks complete on acknowledgment (SRS-706)
- Assignment worker creates tasks for all affected members (SRS-701)
- Overdue worker sends notifications for past-due tasks (SRS-701)

## 6. Test Results Summary

| Metric      | Value   |
| ----------- | ------- |
| Total Cases | Pending |
| Passed      | Pending |
| Failed      | Pending |
| Pass Rate   | Pending |

## 7. Traceability

| Direction | Link Type | Target ID | Title                                                |
| --------- | --------- | --------- | ---------------------------------------------------- |
| Up        | verifies  | SRS-701   | Auto-Assign Training on Change Control Effectiveness |
| Up        | verifies  | SRS-705   | Complete Quiz-Based Training                         |
| Up        | verifies  | SRS-706   | Complete Acknowledge-Based Training                  |
