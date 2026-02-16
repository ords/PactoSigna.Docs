---
id: TC-007
title: 'Document Management — API & Service Tests'
status: draft
links:
  - type: verified-by
    target: SRS-301
  - type: verified-by
    target: SRS-305
  - type: verified-by
    target: SRS-306
---

# Document Management — API & Service Tests

## 1. Purpose

Verifies the HTTP API layer and service logic for document CRUD operations, including the documents service, controller, API client, and Firestore repository. These tests bridge the sync engine output to the frontend-facing API.

## 2. Test Type

| Field         | Value                               |
| ------------- | ----------------------------------- |
| Document Type | Test Report (TEST)                  |
| Test Level    | Unit                                |
| Test Method   | Automated (Vitest)                  |
| Environment   | Local (vitest), CI (GitHub Actions) |

## 3. Scope

### In Scope

- Documents service business logic (list, get, filter)
- Documents controller HTTP endpoint handling
- Documents API client (frontend service layer)
- Documents Firestore repository queries
- Shared schema validation for document entities

### Out of Scope

- Sync engine and parsing (see TC-006)
- Frontend page rendering (see TC-008)

## 4. Test Coverage

| Test File                                                          | Package              | Verifies                                      |
| ------------------------------------------------------------------ | -------------------- | --------------------------------------------- |
| `apps/services/src/modules/documents/DocumentsService.test.ts`     | @pactosigna/services | Document listing, filtering, detail retrieval |
| `apps/services/src/modules/documents/DocumentsController.test.ts`  | @pactosigna/services | HTTP request validation and response shaping  |
| `apps/web/src/shared/services/api/__tests__/documents-api.test.ts` | @pactosigna/web      | Documents REST API client functions           |
| `packages/data/src/repositories/DocumentsRepository.test.ts`       | @pactosigna/data     | Firestore document queries and persistence    |
| `packages/shared/src/schemas/schemas.test.ts`                      | @pactosigna/shared   | Shared Zod schemas for document validation    |

## 5. Pass Criteria

- Documents service returns synced documents with correct metadata (SRS-301)
- Document list endpoint supports pagination and filtering by type (SRS-305)
- Document detail endpoint returns full document with content and links (SRS-306)
- API client correctly maps request/response types from contracts (SRS-305, SRS-306)
- Repository queries use correct Firestore indexes (SRS-301)

## 6. Test Results Summary

| Metric      | Value   |
| ----------- | ------- |
| Total Cases | Pending |
| Passed      | Pending |
| Failed      | Pending |
| Pass Rate   | Pending |

## 7. Traceability

| Direction | Link Type | Target ID | Title                  |
| --------- | --------- | --------- | ---------------------- |
| Up        | verifies  | SRS-301   | Sync Document Metadata |
| Up        | verifies  | SRS-305   | Document List View     |
| Up        | verifies  | SRS-306   | Document Detail View   |
