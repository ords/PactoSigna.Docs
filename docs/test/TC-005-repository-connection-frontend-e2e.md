---
id: TC-005
title: 'Repository Connection — Frontend & E2E Tests'
status: draft
links:
  - type: verified-by
    target: SRS-202
  - type: verified-by
    target: SRS-203
  - type: verified-by
    target: SRS-205
---

# Repository Connection — Frontend & E2E Tests

## 1. Purpose

Verifies the frontend components, pages, hooks, and API client for repository connection and management. Includes E2E journey testing the full GitHub integration flow from connecting to viewing sync status.

## 2. Test Type

| Field         | Value                                        |
| ------------- | -------------------------------------------- |
| Document Type | Test Report (TEST)                           |
| Test Level    | Unit / System (E2E)                          |
| Test Method   | Automated (Vitest + Playwright BDD)          |
| Environment   | Local (vitest), Firebase Emulators (E2E), CI |

## 3. Scope

### In Scope

- Repository list page and components
- Connect repository dialog workflow
- Repository selector component
- GitHub connect prompt and error states
- Repository context provider
- Sync health utilities
- Settings repositories tab hook
- E2E: Repository connection journey

### Out of Scope

- Backend repository service (see TC-004)
- Webhook processing (see TC-004)

## 4. Test Coverage

| Test File                                                                                  | Package         | Verifies                                   |
| ------------------------------------------------------------------------------------------ | --------------- | ------------------------------------------ |
| `apps/web/src/shared/services/api/__tests__/repositories-api.test.ts`                      | @pactosigna/web | Repository REST API client                 |
| `apps/web/src/features/repositories/pages/__tests__/RepositoriesPage.test.tsx`             | @pactosigna/web | Repositories list page rendering           |
| `apps/web/src/features/repositories/components/__tests__/RepositoryList.test.tsx`          | @pactosigna/web | Repository list component                  |
| `apps/web/src/features/repositories/components/__tests__/GitHubConnectPrompt.test.tsx`     | @pactosigna/web | GitHub App installation prompt             |
| `apps/web/src/features/repositories/components/__tests__/RepositorySelector.test.tsx`      | @pactosigna/web | Repository selection dropdown              |
| `apps/web/src/features/repositories/components/__tests__/ConnectRepositoryDialog.test.tsx` | @pactosigna/web | Connect repository modal dialog            |
| `apps/web/src/features/repositories/components/__tests__/HowToAdaptPanel.test.tsx`         | @pactosigna/web | Repository adaptation guidance panel       |
| `apps/web/src/features/repositories/components/__tests__/GitHubErrorMessage.test.tsx`      | @pactosigna/web | GitHub error state display                 |
| `apps/web/src/features/repositories/components/__tests__/RepositoriesEmptyState.test.tsx`  | @pactosigna/web | Empty state when no repositories connected |
| `apps/web/src/features/repositories/components/__tests__/ViewSamplesModal.test.tsx`        | @pactosigna/web | Sample repository viewer                   |
| `apps/web/src/features/repositories/context/__tests__/RepositoryContext.test.tsx`          | @pactosigna/web | Repository context provider                |
| `apps/web/src/features/settings/hooks/__tests__/useRepositoriesTab.test.ts`                | @pactosigna/web | Settings repositories tab hook             |
| `apps/web/src/features/settings/utils/syncHealth.test.ts`                                  | @pactosigna/web | Sync health status utilities               |
| `e2e/features/journeys/02-repository-connection.feature`                                   | e2e             | Full repository connection E2E journey     |

## 5. Pass Criteria

- Repository list renders connected repositories with sync status (SRS-203, SRS-205)
- Connect dialog allows selecting and confirming repos (SRS-202)
- Sync health utility correctly computes sync freshness (SRS-205)
- Repository context provides current repo to child components (SRS-203)
- E2E: User can connect a repository and see it listed (SRS-202, SRS-203)

## 6. Test Results Summary

| Metric      | Value   |
| ----------- | ------- |
| Total Cases | Pending |
| Passed      | Pending |
| Failed      | Pending |
| Pass Rate   | Pending |

## 7. Traceability

| Direction | Link Type | Target ID | Title                         |
| --------- | --------- | --------- | ----------------------------- |
| Up        | verifies  | SRS-202   | Select Repositories           |
| Up        | verifies  | SRS-203   | Manage Connected Repositories |
| Up        | verifies  | SRS-205   | Sync Status Dashboard         |
