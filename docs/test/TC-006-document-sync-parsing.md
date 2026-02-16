---
id: TC-006
title: 'Document Management — Sync Engine & Parsing Tests'
status: draft
links:
  - type: verified-by
    target: SRS-301
  - type: verified-by
    target: SRS-302
  - type: verified-by
    target: SRS-303
  - type: verified-by
    target: SRS-304
---

# Document Management — Sync Engine & Parsing Tests

## 1. Purpose

Verifies the core document sync engine, markdown parsing, document type inference, link extraction, and validation logic that runs in Cloud Functions. These are the foundational backend components for the Document Management bounded context.

## 2. Test Type

| Field         | Value                               |
| ------------- | ----------------------------------- |
| Document Type | Test Report (TEST)                  |
| Test Level    | Unit                                |
| Test Method   | Automated (Vitest)                  |
| Environment   | Local (vitest), CI (GitHub Actions) |

## 3. Scope

### In Scope

- Sync engine orchestration (fetch → parse → persist)
- Markdown document parsing and frontmatter extraction
- Document validation rules
- Document type pattern matching and inference
- Markdown link parsing and extraction
- Link sync (persisting extracted links to Firestore)
- Repository snapshot creation during sync
- Gap detection logic (missing required links)

### Out of Scope

- Sync trigger API (see TC-004)
- Frontend document views (see TC-008)
- E2E sync flow (see TC-008)

## 4. Test Coverage

| Test File                                                  | Package               | Verifies                                        |
| ---------------------------------------------------------- | --------------------- | ----------------------------------------------- |
| `apps/functions/src/shared/sync-engine.test.ts`            | @pactosigna/functions | Full sync engine orchestration                  |
| `apps/functions/src/shared/document-parser.test.ts`        | @pactosigna/functions | Markdown parsing and frontmatter extraction     |
| `apps/functions/src/shared/document-validation.test.ts`    | @pactosigna/functions | Document schema and content validation          |
| `apps/functions/src/shared/document-type-patterns.test.ts` | @pactosigna/functions | Document type inference from file paths/content |
| `apps/functions/src/shared/markdown-link-parser.test.ts`   | @pactosigna/functions | Link extraction from markdown content           |
| `apps/functions/src/shared/link-sync.test.ts`              | @pactosigna/functions | Persisting extracted links to Firestore         |
| `apps/functions/src/shared/gap-detection.test.ts`          | @pactosigna/functions | Detecting missing required traceability links   |
| `apps/functions/src/shared/repository-snapshot.test.ts`    | @pactosigna/functions | Snapshot creation during sync for changelog     |

## 5. Pass Criteria

- Sync engine fetches markdown from GitHub, parses, and persists documents (SRS-301)
- Document type is inferred from frontmatter or file path patterns (SRS-302)
- Changelog entries are created on document changes between syncs (SRS-303)
- Links are extracted from markdown content and stored as link records (SRS-304)
- Gap detection identifies documents missing required links (SRS-304, SRS-309)

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
| Up        | verifies  | SRS-301   | Sync Document Metadata   |
| Up        | verifies  | SRS-302   | Document Type Inference  |
| Up        | verifies  | SRS-303   | Track Document Changelog |
| Up        | verifies  | SRS-304   | Extract and Store Links  |
