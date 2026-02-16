---
id: TC-004
title: 'Repository Connection — Backend & Webhook Tests'
status: draft
links:
  - type: verified-by
    target: SRS-201
  - type: verified-by
    target: SRS-202
  - type: verified-by
    target: SRS-203
  - type: verified-by
    target: SRS-204
  - type: verified-by
    target: SRS-206
---

# Repository Connection — Backend & Webhook Tests

## 1. Purpose

Verifies the backend services and controllers for GitHub App integration, repository management, sync orchestration, and webhook event handling. These tests cover the Repository Connection bounded context's server-side logic.

## 2. Test Type

| Field         | Value                               |
| ------------- | ----------------------------------- |
| Document Type | Test Report (TEST)                  |
| Test Level    | Unit                                |
| Test Method   | Automated (Vitest)                  |
| Environment   | Local (vitest), CI (GitHub Actions) |

## 3. Scope

### In Scope

- Repository CRUD operations (connect, list, update, disconnect)
- Sync trigger and Cloud Tasks queue integration
- GitHub webhook event processing (push, installation)
- GitHub controller request handling
- Repository service sync logic

### Out of Scope

- Frontend repository UI components (see TC-005)
- E2E repository connection flow (see TC-005)
- Actual GitHub API calls (mocked in tests)

## 4. Test Coverage

| Test File                                                                 | Package              | Verifies                             |
| ------------------------------------------------------------------------- | -------------------- | ------------------------------------ |
| `apps/services/src/modules/repositories/RepositoriesService.test.ts`      | @pactosigna/services | Repository CRUD and management       |
| `apps/services/src/modules/repositories/RepositoriesService.sync.test.ts` | @pactosigna/services | Sync trigger and queue dispatch      |
| `apps/services/src/modules/repositories/RepositoriesController.test.ts`   | @pactosigna/services | HTTP endpoint validation and routing |
| `apps/services/src/modules/github/GitHubWebhookService.test.ts`           | @pactosigna/services | Webhook event parsing and processing |
| `apps/services/src/modules/github/GitHubController.test.ts`               | @pactosigna/services | GitHub API proxy endpoints           |

## 5. Pass Criteria

- GitHub App installation callback creates repository records (SRS-201)
- Repository selection persists chosen repos to Firestore (SRS-202)
- Connected repositories can be listed, updated, and disconnected (SRS-203)
- Webhook events (push, installation_repositories) trigger appropriate sync actions (SRS-204)
- App uninstallation webhook marks repositories as disconnected (SRS-206)

## 6. Test Results Summary

| Metric      | Value   |
| ----------- | ------- |
| Total Cases | Pending |
| Passed      | Pending |
| Failed      | Pending |
| Pass Rate   | Pending |

## 7. Traceability

| Direction | Link Type | Target ID | Title                            |
| --------- | --------- | --------- | -------------------------------- |
| Up        | verifies  | SRS-201   | Install GitHub App               |
| Up        | verifies  | SRS-202   | Select Repositories              |
| Up        | verifies  | SRS-203   | Manage Connected Repositories    |
| Up        | verifies  | SRS-204   | GitHub Webhook Handler           |
| Up        | verifies  | SRS-206   | Handle GitHub App Uninstallation |
