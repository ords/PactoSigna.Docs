# PDF Report Support Design

**Issue:** PactoSigna/Sample.QMS.ISO13485#1
**Date:** 2026-02-10
**Status:** Approved

## Problem

External parties share PDF reports (audit reports, penetration testing docs) that need electronic signatures and traceability. Converting them to markdown is impractical. The system currently only handles markdown documents synced from GitHub.

## Solution: Markdown Wrapper Pattern

A PDF-backed document is a regular PactoSigna document whose content is a PDF file instead of inline markdown. It is defined by a standard markdown wrapper file with a `source` field in frontmatter pointing to a co-located PDF stored in Git LFS.

### Example Wrapper File

```markdown
---
id: AUD-001
title: Annual Audit Report 2025
docType: external_report
status: approved
source: ./AUD-001-report.pdf
reviewers:
  - alice@example.com
approvers:
  - bob@example.com
---

External audit report from Acme Auditors covering ISO 13485 compliance.

**Verifies:** [SRS-001](../requirements/SRS-001.md), [SRS-005](../requirements/SRS-005.md)
**Related to:** [SOP-003](../procedures/SOP-003.md)
```

### Why Markdown Wrappers (Not Sidecar YAML)

- Zero new concepts for users who already write frontmatter
- The markdown body holds outbound traceability links and contextual notes
- The existing sync engine already processes `.md` files
- The existing link parser extracts references from the body unchanged

## Key Decisions

### Traceability: Document-Level Only

PDFs are opaque. Section-level linking into or out of PDF content is not feasible.

- **Links into PDF documents** work at document level (e.g., link to `AUD-001`), same as any other document
- **Links out of PDF documents** are declared manually in the wrapper's markdown body using existing link syntax (e.g., `**Verifies:** [SRS-001](...)`)
- The existing link parser handles both directions unchanged
- The UI shows a subtle indicator that this is an external document with document-level traceability only

### Storage: Git LFS Only

PDFs stay in Git LFS. PactoSigna does not store PDF content on its platform. PDFs are fetched on-demand from GitHub when a user opens the document. This aligns with the principle that PactoSigna reads from the user's repo without duplicating content.

### API: Separate PDF Endpoint

A dedicated `GET /api/v2/documents/:documentId/pdf` endpoint serves PDF content. This is separate from the existing markdown content endpoint for clean separation and independent evolution.

### Frontend: Reuse @react-pdf-viewer/core

The signing requests feature already installed `@react-pdf-viewer/core`, `@react-pdf-viewer/default-layout`, and `pdfjs-dist`. The document detail page reuses the same `<Worker>` + `<Viewer>` pattern from `FieldPlacementEditor.tsx` for rendering PDFs with zoom, scroll, and search.

## Architecture Changes

### Document Model

Two new fields on `DocumentModel`:

- `sourceType: 'markdown' | 'pdf'` — defaults to `'markdown'` for existing documents
- `sourcePath: string | null` — resolved repo path to the PDF file (e.g., `docs/reports/AUD-001-report.pdf`)

No new collections or models. No migration needed; next sync populates fields naturally.

### Document Parser (`document-parser.ts`)

When parsing frontmatter:

1. Check if `source` field is present and ends with `.pdf`
2. Resolve the relative path against the wrapper file's directory
3. Return `sourceType: 'pdf'` and the resolved `sourcePath`

### Sync Engine (`sync-engine.ts`)

1. File discovery unchanged — still only scans for `.md` files
2. For PDF-backed documents, validate the referenced PDF exists in the git tree
3. Store wrapper markdown body as `content` (contains traceability links and notes)
4. Write `sourceType` and `sourcePath` to the Firestore document record

### GitHub Service (`GitHubService.ts`)

New method `getFileBinaryContent()`:

- Uses Octokit with `mediaType: { format: 'raw' }` to resolve LFS pointers
- Returns a `Buffer` instead of a UTF-8 string
- GitHub Contents API supports files up to 100MB via raw media type
- 25MB limit enforced for consistency with signing requests

### API Route

New endpoint: `GET /api/v2/documents/:documentId/pdf?commitSha=...`

1. Look up document by frontmatter ID
2. Verify `sourceType === 'pdf'`
3. Fetch binary content from GitHub using `sourcePath` at the given commit SHA
4. Stream response with `Content-Type: application/pdf`
5. Set caching headers (content is immutable at a given commit SHA)

### Frontend

**Document detail page** branches on `sourceType`:

- `markdown` — Existing `react-markdown` rendering, unchanged
- `pdf` — Two-part layout:
  1. Wrapper notes: render the wrapper markdown body above the viewer (contextual notes + outbound links)
  2. PDF viewer: `<Worker>` + `<Viewer>` from `@react-pdf-viewer/core`

**New hook:** `useDocumentPdf(documentId, commitSha)` — fetches the PDF blob from the `/pdf` endpoint, creates and manages the blob URL lifecycle.

### Contracts

Add `sourceType` and `sourcePath` to the document response DTO so the frontend knows how to render.

## Out of Scope

- **PDF upload through PactoSigna UI** — Users add PDFs to their git repo via git push with LFS
- **PDF text extraction / AI summarization** — Wrapper body is manually authored
- **Section-level traceability** — Document-level only for PDF-backed documents
- **PDF annotation or editing** — Viewer is read-only
- **Non-PDF binary files** — Only `.pdf` supported via `source` field for now
- **New document type enum** — `sourceType` is orthogonal to `docType`

## Implementation Sequence

1. Model + contracts: add `sourceType` and `sourcePath` fields
2. Document parser: detect `source` frontmatter, resolve path
3. Sync engine: validate PDF in git tree, write new fields
4. GitHub service: add `getFileBinaryContent()` with LFS support
5. API endpoint: new `/pdf` route
6. Frontend hook: `useDocumentPdf()`
7. Frontend page: branch rendering on `sourceType`, PDF viewer component
