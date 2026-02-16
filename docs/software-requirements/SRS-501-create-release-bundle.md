---
id: SRS-501
title: 'Create Release Bundle'
status: draft
links:
  - type: derives-from
    target: PRS-011
---

# Create Release Bundle

## Requirement

> **SRS-501.1:** The system **SHALL** allow Quality Managers to create a new release with a name, version, and description, and select documents from any connected repository.
>
> **SRS-501.2:** The system **SHALL** lock selected documents to their current commit SHA at the time of inclusion in the release.
>
> **SRS-501.3:** The system **SHALL** create the release in "draft" status with a type of 'qms', 'device', or 'combined'.
>
> **SRS-501.4:** The system **SHALL** create an audit log entry for release creation.

## Context

Release bundles group related document changes for review and approval. The release captures each document's ID, path, title, and commit SHA to create an immutable snapshot. For device releases, the specific device repository must be selected.

## Classification

| Field           | Value              |
| --------------- | ------------------ |
| Safety Class    | B                  |
| Category        | Functional         |
| Bounded Context | Release Management |

## Detailed Specification

### Inputs

- Release name (required)
- Release version (required)
- Release description (optional)
- Release type: 'qms', 'device', or 'combined'
- Device repository ID (for device releases)
- Selected document IDs

### Processing

1. Capture current commit SHA for each selected document.
2. Create release document in Firestore with status 'draft'.
3. Store changes array with document metadata and locked SHAs.
4. Create audit log entry.

### Outputs

- New release document in Firestore (status: draft)
- Audit log entry for `release.created`

### Error Handling

- Missing required fields: display validation error
- Document no longer exists: warn and exclude from release
- Duplicate release version: warn but allow

## Acceptance Criteria

1. **Given** a Quality Manager, **when** they create a release with a name, version, and selected documents, **then** a new draft release is created.
2. **Given** selected documents, **when** added to a release, **then** each document is locked to its current commit SHA.
3. **Given** a new release, **when** creation succeeds, **then** the release status is "draft".
4. **Given** release creation, **when** completed, **then** an audit log entry with action `release.created` is recorded.

## Traceability

| Direction | Link Type    | Target ID | Title                            |
| --------- | ------------ | --------- | -------------------------------- |
| Up        | derives-from | PRS-011    | Release creation & configuration |

## Source

**STORY-501 — Create Release Bundle**

> **As a** Quality Manager **I want** to create a release bundle with selected documents **So that** I can group related changes for review and approval.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
