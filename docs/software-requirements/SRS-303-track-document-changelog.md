---
id: SRS-303
title: 'Track Document Changelog'
status: draft
links:
  - type: derives-from
    target: PRS-006
---

# Track Document Changelog

## Requirement

> **SRS-303.1:** The system **SHALL** create an immutable changelog entry in a subcollection (`/documents/{docId}/changelog/{entryId}`) for each modified document during every sync operation.
>
> **SRS-303.2:** The system **SHALL** record in each changelog entry: commit SHA, previous commit SHA, change type, author email, matched member ID (if available), timestamp, pull request number and title (if available), and metadata changes (field-level diffs).
>
> **SRS-303.3:** The system **SHALL** provide a changelog view for any document showing the history of changes in chronological order.

## Context

Document changelog provides audit traceability for every modification. Changelog entries are immutable once created. Commit author email is matched against organization members where possible. The changelog is displayed on the document detail page and can indicate which release included each change.

## Classification

| Field           | Value               |
| --------------- | ------------------- |
| Safety Class    | B                   |
| Category        | Data                |
| Bounded Context | Document Management |

## Detailed Specification

### Inputs

- Sync event details (commit SHA, changed files)
- Git commit metadata (author email, timestamp, PR info)

### Processing

1. For each modified document, create a changelog entry.
2. Match commit author email to organization members.
3. Record before/after metadata changes at the field level.
4. Store in immutable subcollection.

### Outputs

- Changelog entries in `/documents/{docId}/changelog/{entryId}`
- Each entry includes full provenance metadata

### Error Handling

- Author email not matched to a member: store email, set member ID to null
- Missing PR metadata: store null for PR fields

## Acceptance Criteria

1. **Given** a document modified during sync, **when** the sync completes, **then** a changelog entry is created with commit SHA, author, and timestamp.
2. **Given** a changelog entry, **when** any attempt to modify or delete it occurs, **then** the operation is rejected (entries are immutable).
3. **Given** a document with multiple changelog entries, **when** a user views the changelog, **then** entries are displayed in chronological order.
4. **Given** a commit with a pull request, **when** the changelog entry is created, **then** the PR number and title are included.

## Traceability

| Direction | Link Type    | Target ID | Title                |
| --------- | ------------ | --------- | -------------------- |
| Up        | derives-from | PRS-006    | Document sync engine |

## Source

**STORY-303 — Track Document Changelog**

> **As a** member **I want** to see the history of changes to a document **So that** I can understand how it evolved.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
