---
id: TC-008
title: 'Document Management — Frontend & E2E Tests'
status: draft
links:
  - type: verified-by
    target: SRS-305
  - type: verified-by
    target: SRS-306
  - type: verified-by
    target: SRS-308
  - type: verified-by
    target: SRS-309
  - type: verified-by
    target: SRS-310
---

# Document Management — Frontend & E2E Tests

## 1. Purpose

Verifies frontend pages, components, and utilities for document browsing, detail views, traceability matrix, and device switching. Includes E2E journeys for document sync and traceability workflows.

## 2. Test Type

| Field         | Value                                        |
| ------------- | -------------------------------------------- |
| Document Type | Test Report (TEST)                           |
| Test Level    | Unit / System (E2E)                          |
| Test Method   | Automated (Vitest + Playwright BDD)          |
| Environment   | Local (vitest), Firebase Emulators (E2E), CI |

## 3. Scope

### In Scope

- Documents list page rendering and filtering
- Document detail page with markdown rendering
- Traceability matrix page
- Mermaid diagram rendering component
- Frontmatter stripping utility
- Device/QMS service tests (device switcher backend)
- E2E: Document sync journey
- E2E: Document traceability journey

### Out of Scope

- Sync engine internals (see TC-006)
- Document API service layer (see TC-007)

## 4. Test Coverage

| Test File                                                                     | Package              | Verifies                                 |
| ----------------------------------------------------------------------------- | -------------------- | ---------------------------------------- |
| `apps/web/src/features/documents/pages/__tests__/DocumentsPage.test.tsx`      | @pactosigna/web      | Document list page rendering             |
| `apps/web/src/features/documents/pages/__tests__/DocumentDetailPage.test.tsx` | @pactosigna/web      | Document detail page with content        |
| `apps/web/src/features/documents/pages/__tests__/TraceabilityPage.test.tsx`   | @pactosigna/web      | Traceability matrix display              |
| `apps/web/src/shared/components/__tests__/MermaidDiagram.test.tsx`            | @pactosigna/web      | Mermaid diagram rendering in documents   |
| `apps/web/src/shared/utils/stripFrontmatter.test.ts`                          | @pactosigna/web      | YAML frontmatter stripping from markdown |
| `apps/services/src/modules/devices/DevicesService.test.ts`                    | @pactosigna/services | Device listing and switching             |
| `apps/services/src/modules/qms/QmsService.test.ts`                            | @pactosigna/services | QMS context management                   |
| `e2e/features/journeys/07-document-sync.feature`                              | e2e                  | Full document sync E2E journey           |
| `e2e/features/journeys/05-document-traceability.feature`                      | e2e                  | Document traceability E2E journey        |

## 5. Pass Criteria

- Documents page renders list with type filtering and search (SRS-305)
- Document detail page shows rendered markdown with correct metadata (SRS-306)
- Traceability page displays link matrix between documents (SRS-308)
- Gap detection is surfaced in traceability view (SRS-309)
- Device switcher changes active device context (SRS-310)
- E2E: Documents appear after sync completes (SRS-301, SRS-305)
- E2E: Traceability links are visible between documents (SRS-308)

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
| Up        | verifies  | SRS-305   | Document List View       |
| Up        | verifies  | SRS-306   | Document Detail View     |
| Up        | verifies  | SRS-308   | Traceability Matrix View |
| Up        | verifies  | SRS-309   | Gap Detection            |
| Up        | verifies  | SRS-310   | Device Switcher          |
