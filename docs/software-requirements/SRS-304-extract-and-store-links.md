---
id: SRS-304
title: 'Extract and Store Links'
status: draft
links:
  - type: derives-from
    target: PRS-007
---

# Extract and Store Links

## Requirement

> **SRS-304.1:** The system **SHALL** parse the `links` array from document frontmatter and create/update Link documents in Firestore at `/organizations/{orgId}/links/{linkId}`.
>
> **SRS-304.2:** The system **SHALL** support the following link types: `derives-from`, `implements`, `verified-by`, `mitigates`, `parent-of`, and `related-to`.
>
> **SRS-304.3:** The system **SHALL** remove links that no longer exist in the updated frontmatter (delete old links for source document, create new ones).
>
> **SRS-304.4:** The system **SHALL** validate that link targets exist and log a warning (non-blocking) when a target is not found.

## Context

Traceability links are the backbone of regulatory compliance. Links are extracted from YAML frontmatter during sync and stored as first-class entities in Firestore for efficient querying. On each sync, old links for a source document are deleted and replaced with the current set. The `LinksUpdated` domain event is fired.

## Classification

| Field           | Value               |
| --------------- | ------------------- |
| Safety Class    | B                   |
| Category        | Data                |
| Bounded Context | Document Management |

## Detailed Specification

### Inputs

- `links` array from document frontmatter
- Source document ID and path
- Target document IDs

### Processing

1. Parse `links` array from frontmatter.
2. Delete all existing links where the current document is the source.
3. Create new Link documents for each entry in the `links` array.
4. Validate target document existence (log warning if not found).
5. Fire `LinksUpdated` domain event.

### Outputs

- Link documents in Firestore (`/organizations/{orgId}/links/{linkId}`)
- Each link includes: sourceDocumentId, targetDocumentId, linkType, source/target paths, createdAt

### Error Handling

- Target document not found: log warning, create link anyway (target may be synced later)
- Invalid link type: log error, skip link
- Malformed links array: log error, process valid entries

## Acceptance Criteria

1. **Given** a document with a `links` array in frontmatter, **when** synced, **then** Link documents are created in Firestore for each entry.
2. **Given** a document whose `links` array is modified (link removed), **when** synced, **then** the removed link is deleted from Firestore.
3. **Given** a link with a target that doesn't exist yet, **when** synced, **then** the link is created and a warning is logged.
4. **Given** a link of type `verified-by`, **when** synced, **then** the link type is stored correctly as `verified-by`.

## Traceability

| Direction | Link Type    | Target ID | Title              |
| --------- | ------------ | --------- | ------------------ |
| Up        | derives-from | PRS-007    | Document lifecycle |

## Source

**STORY-304 — Extract and Store Links**

> **As the** PactoSigna system **I want** to extract traceability links from frontmatter **So that** I can query document relationships.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
