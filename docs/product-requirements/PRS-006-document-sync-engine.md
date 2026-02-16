---
id: PRS-006
title: 'Document Sync Engine'
status: draft
links:
  - type: derives-from
    target: UN-003
---

# Document Sync Engine

## Requirement Statements

1. The system **SHALL** parse YAML frontmatter from markdown files during sync, extracting document metadata including id, title, status, docType, and links arrays.

2. The system **SHALL** infer document type from the file's directory path when the `docType` field is not explicitly set in frontmatter (e.g., files in `/sops/` are classified as SOP, `/requirements/` as Requirement).

3. The system **SHALL** create immutable changelog entries for each synced document recording the commit SHA, change type (added, modified, removed), author (from Git commit), and timestamp.

4. The system **SHALL** extract frontmatter `links` arrays and store them as queryable Link documents in Firestore, enabling fast traceability queries.

5. The system **SHALL** process sync operations via background task queue (Google Cloud Tasks) to avoid blocking API responses.

6. The system **SHALL** track sync progress through sync events with status transitions: pending → in_progress → completed | failed.

7. The system **SHALL** mark deleted files as removed in Firestore while preserving their full history and previous metadata.

8. The system **SHALL** handle malformed frontmatter gracefully by logging a warning, storing the document with available metadata, and flagging the parse error in the sync event details.

## Rationale

The document sync engine is the bridge between GitHub (source of truth for content) and Firestore (source of truth for metadata). Accurate frontmatter parsing ensures traceability links are machine-readable and queryable. Immutable changelog entries provide the version history needed for audit trails. Background processing via Cloud Tasks ensures the system remains responsive during large sync operations. Graceful error handling prevents a single malformed document from blocking an entire repository sync.

## Classification

| Field    | Value                                   |
| -------- | --------------------------------------- |
| Priority | Essential                               |
| Category | Functional                              |
| Standard | ISO 13485 §4.2.4 (control of documents) |

## Acceptance Criteria

1. Given a markdown file with valid YAML frontmatter, when synced, then all frontmatter fields (id, title, status, docType, links) are correctly stored in the Firestore document record
2. Given a markdown file in `/requirements/` without an explicit `docType` in frontmatter, when synced, then the document type is inferred as "Requirement"
3. Given a sync that includes a modified file, when the changelog is queried, then an entry exists with the correct commit SHA, change type "modified", Git author, and timestamp
4. Given a frontmatter with a `links` array containing `[{type: "derives-from", target: "UN-001"}]`, when synced, then a Link document is created in Firestore with sourceDocId, targetDocId, and linkType fields
5. Given a sync request, when the Cloud Task is queued, then the sync event status progresses from pending to in_progress to completed (or failed)
6. Given a file deleted from the repository, when synced, then the document is marked as removed in Firestore but all historical data (changelog, links, metadata) is preserved
7. Given a file with invalid YAML frontmatter, when synced, then the document is stored with available metadata, a parse warning is logged, and the sync event completes without failing

## Traceability

| Direction | Link Type    | Target ID | Title                       |
| --------- | ------------ | --------- | --------------------------- |
| Up        | derives-from | UN-003    | Document Management & Sync  |
| Down      | refined-by   | —         | _(To be linked by SWR-xxx)_ |
| Down      | verified-by  | —         | _(To be linked by TP-xxx)_  |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
