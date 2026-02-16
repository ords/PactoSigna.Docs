---
id: TC-011
title: 'Release Management — Backend Tests'
status: draft
links:
  - type: verified-by
    target: SRS-501
  - type: verified-by
    target: SRS-502
  - type: verified-by
    target: SRS-503
  - type: verified-by
    target: SRS-506
  - type: verified-by
    target: SRS-507
---

# Release Management — Backend Tests

## 1. Purpose

Verifies the backend services and controllers for the release lifecycle: creation, editing, submission for review, approval, and publishing. Includes change control and product release service tests.

## 2. Test Type

| Field         | Value                               |
| ------------- | ----------------------------------- |
| Document Type | Test Report (TEST)                  |
| Test Level    | Unit                                |
| Test Method   | Automated (Vitest)                  |
| Environment   | Local (vitest), CI (GitHub Actions) |

## 3. Scope

### In Scope

- Release CRUD (create, edit draft, submit, approve, publish)
- Release state machine transitions
- Change control service (create change control records)
- Product releases service (cross-device release coordination)
- Releases controller HTTP endpoints
- Releases Firestore repository

### Out of Scope

- Frontend release pages (see TC-012)
- E2E release workflows (see TC-013)
- Signature collection during release (see TC-014)

## 4. Test Coverage

| Test File                                                                  | Package              | Verifies                               |
| -------------------------------------------------------------------------- | -------------------- | -------------------------------------- |
| `apps/services/src/modules/releases/ReleasesService.test.ts`               | @pactosigna/services | Release lifecycle service logic        |
| `apps/services/src/modules/releases/ReleasesController.test.ts`            | @pactosigna/services | Release HTTP endpoint handling         |
| `apps/services/src/modules/changeControls/ChangeControlsService.test.ts`   | @pactosigna/services | Change control creation and management |
| `apps/services/src/modules/productReleases/ProductReleasesService.test.ts` | @pactosigna/services | Product release coordination           |
| `packages/data/src/repositories/ReleasesRepository.test.ts`                | @pactosigna/data     | Release Firestore queries              |

## 5. Pass Criteria

- Release creation produces a draft release with document snapshot (SRS-501)
- Draft releases can be edited (update title, documents, notes) (SRS-502)
- Submitting a release transitions it to "in_review" status (SRS-503)
- Approving a release requires all required signatures (SRS-506)
- Publishing a release freezes it and triggers post-publish actions (SRS-507)

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
| Up        | verifies  | SRS-501   | Create Release Bundle     |
| Up        | verifies  | SRS-502   | Edit Draft Release        |
| Up        | verifies  | SRS-503   | Submit Release for Review |
| Up        | verifies  | SRS-506   | Approve Release           |
| Up        | verifies  | SRS-507   | Publish Release           |
