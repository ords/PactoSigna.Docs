---
id: TC-017
title: 'Training Management — Frontend Tests'
status: draft
links:
  - type: verified-by
    target: SRS-702
  - type: verified-by
    target: SRS-703
  - type: verified-by
    target: SRS-704
  - type: verified-by
    target: SRS-709
---

# Training Management — Frontend Tests

## 1. Purpose

Verifies the frontend training API client that supports training task listing, detail viewing, task start/completion, and admin dashboard access. The training API test covers the REST client layer used by training feature pages.

## 2. Test Type

| Field         | Value                               |
| ------------- | ----------------------------------- |
| Document Type | Test Report (TEST)                  |
| Test Level    | Unit                                |
| Test Method   | Automated (Vitest)                  |
| Environment   | Local (vitest), CI (GitHub Actions) |

## 3. Scope

### In Scope

- Training REST API client functions (list, get, start, complete)
- Admin training dashboard API calls
- Training task detail retrieval

### Out of Scope

- Backend training service (see TC-016)
- E2E training flows (see TC-018)
- Training page components (not yet covered by unit tests — gap)

## 4. Test Coverage

| Test File                                                         | Package         | Verifies                           |
| ----------------------------------------------------------------- | --------------- | ---------------------------------- |
| `apps/web/src/shared/services/api/__tests__/training-api.test.ts` | @pactosigna/web | Training REST API client functions |

## 5. Pass Criteria

- API client fetches training task list for current user (SRS-702)
- API client fetches training task detail with enrichment (SRS-703)
- API client sends start-task request (SRS-704)
- API client supports admin dashboard data fetching (SRS-709)

## 6. Test Results Summary

| Metric      | Value   |
| ----------- | ------- |
| Total Cases | Pending |
| Passed      | Pending |
| Failed      | Pending |
| Pass Rate   | Pending |

## 7. Traceability

| Direction | Link Type | Target ID | Title                     |
| --------- | --------- | --------- | ------------------------- |
| Up        | verifies  | SRS-702   | View My Training Tasks    |
| Up        | verifies  | SRS-703   | View Training Task Detail |
| Up        | verifies  | SRS-704   | Start Training Task       |
| Up        | verifies  | SRS-709   | Admin Training Dashboard  |
