---
id: TC-023
title: 'PDF Export & Reporting Tests'
status: draft
links:
  - type: verified-by
    target: SRS-507
---

# PDF Export & Reporting Tests

## 1. Purpose

Verifies the PDF export pipeline including the export worker, PDF renderer, attestation page generation, zip bundler, cloud storage upload, and email delivery. This pipeline runs as a Cloud Function triggered after release publishing.

## 2. Test Type

| Field         | Value                               |
| ------------- | ----------------------------------- |
| Document Type | Test Report (TEST)                  |
| Test Level    | Unit                                |
| Test Method   | Automated (Vitest)                  |
| Environment   | Local (vitest), CI (GitHub Actions) |

## 3. Scope

### In Scope

- Export worker orchestration (task processing)
- PDF rendering from document content
- Attestation page generation (Part 11 signature details)
- Attestation helper utilities
- Zip bundle creation for multi-document exports
- Cloud Storage upload
- Export email notification with download link
- Export utility functions

### Out of Scope

- Release publishing trigger (see TC-011)
- Frontend download UI (manual verification)

## 4. Test Coverage

| Test File                                               | Package               | Verifies                         |
| ------------------------------------------------------- | --------------------- | -------------------------------- |
| `apps/functions/src/export/worker.test.ts`              | @pactosigna/functions | Export worker task orchestration |
| `apps/functions/src/export/pdf-renderer.test.ts`        | @pactosigna/functions | PDF rendering from markdown/HTML |
| `apps/functions/src/export/attestation.test.ts`         | @pactosigna/functions | Attestation page with signatures |
| `apps/functions/src/export/attestation-helpers.test.ts` | @pactosigna/functions | Attestation utility functions    |
| `apps/functions/src/export/zip-bundler.test.ts`         | @pactosigna/functions | Multi-document zip creation      |
| `apps/functions/src/export/storage.test.ts`             | @pactosigna/functions | Cloud Storage upload             |
| `apps/functions/src/export/email.test.ts`               | @pactosigna/functions | Export completion email          |
| `apps/functions/src/export/utils.test.ts`               | @pactosigna/functions | Export utility functions         |

## 5. Pass Criteria

- Export worker processes task and produces PDF output (SRS-507)
- PDF renderer converts document content to valid PDF (SRS-507)
- Attestation page includes all signer details and timestamps (SRS-507, Part 11)
- Zip bundler packages multiple PDFs into a single archive (SRS-507)
- Exported files are uploaded to Cloud Storage with correct permissions (SRS-507)
- Completion email is sent with download link (SRS-507)

## 6. Test Results Summary

| Metric      | Value   |
| ----------- | ------- |
| Total Cases | Pending |
| Passed      | Pending |
| Failed      | Pending |
| Pass Rate   | Pending |

## 7. Traceability

| Direction | Link Type | Target ID | Title           |
| --------- | --------- | --------- | --------------- |
| Up        | verifies  | SRS-507   | Publish Release |
