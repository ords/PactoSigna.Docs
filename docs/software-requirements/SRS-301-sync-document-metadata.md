---
id: SRS-301
title: 'Sync Document Metadata'
status: draft
links:
  - type: derives-from
    target: PRS-006
---

# Sync Document Metadata

## Requirement

> **SRS-301.1:** The system **SHALL** fetch changed markdown files from GitHub when a webhook is received and parse YAML frontmatter using the `gray-matter` library to extract standard fields (id, title, status, links).
>
> **SRS-301.2:** The system **SHALL** store document metadata in Firestore, updating existing documents (matched by file path) or creating new ones as appropriate.
>
> **SRS-301.3:** The system **SHALL** handle deleted files by marking them as removed in Firestore while preserving their history.
>
> **SRS-301.4:** The system **SHALL** validate required frontmatter fields (id, title) and log sync errors for individual files without failing the entire batch.

## Context

Document sync is the core data pipeline. The `syncDocuments` Cloud Function processes changed files from GitHub. Each document record in Firestore includes repository ID, organization ID, file path, frontmatter fields, current commit SHA, and last synced timestamp. The `DocumentSynced` domain event is fired for each processed document.

## Classification

| Field           | Value               |
| --------------- | ------------------- |
| Safety Class    | A                   |
| Category        | Data                |
| Bounded Context | Document Management |

## Detailed Specification

### Inputs

- Changed file list from GitHub push event
- GitHub repository access (via GitHub App installation)
- YAML frontmatter in markdown files

### Processing

1. Receive list of changed files from webhook handler.
2. Fetch file content from GitHub API.
3. Parse YAML frontmatter with `gray-matter`.
4. Extract: id, title, status, docType, links, and additional metadata.
5. Upsert document records in Firestore (match by file path).
6. Mark removed files as deleted.
7. Fire `DocumentSynced` domain events.

### Outputs

- Document metadata records in Firestore
- Deleted documents marked as removed
- Sync errors logged (per-file, non-blocking)

### Error Handling

- Missing required frontmatter (id or title): log warning, skip file, continue batch
- GitHub API failure: retry via Cloud Tasks
- Malformed YAML: log parse error, skip file
- Firestore write failure: log error, continue with remaining files

## Acceptance Criteria

1. **Given** a push event with modified markdown files, **when** the sync processes, **then** document metadata is extracted from frontmatter and stored in Firestore.
2. **Given** a new markdown file added to the repository, **when** synced, **then** a new document record is created in Firestore.
3. **Given** a deleted file in the push event, **when** synced, **then** the document is marked as removed in Firestore while history is preserved.
4. **Given** a file with missing required frontmatter fields, **when** synced, **then** the file is skipped with a logged error and remaining files continue processing.

## Traceability

| Direction | Link Type    | Target ID | Title                |
| --------- | ------------ | --------- | -------------------- |
| Up        | derives-from | PRS-006    | Document sync engine |

## Source

**STORY-301 — Sync Document Metadata**

> **As the** PactoSigna system **I want** to parse and index markdown frontmatter **So that** documents are searchable and queryable.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
