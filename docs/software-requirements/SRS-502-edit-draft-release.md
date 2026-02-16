---
id: SRS-502
title: 'Edit Draft Release'
status: draft
links:
  - type: derives-from
    target: PRS-011
---

# Edit Draft Release

## Requirement

> **SRS-502.1:** The system **SHALL** allow adding and removing documents from a release only while the release is in "draft" status.
>
> **SRS-502.2:** The system **SHALL** capture the current commit SHA of each newly added document at the time of addition.
>
> **SRS-502.3:** The system **SHALL** prevent any modifications to a release that has left "draft" status.
>
> **SRS-502.4:** The system **SHALL** create an audit log entry for each draft release modification.

## Context

Draft releases are editable to allow scope adjustments before formal review. Once a release advances beyond draft status, all modifications are prevented to maintain the integrity of the review and signature process. Each add/remove operation is individually audit-logged.

## Classification

| Field           | Value              |
| --------------- | ------------------ |
| Safety Class    | B                  |
| Category        | Functional         |
| Bounded Context | Release Management |

## Detailed Specification

### Inputs

- Release ID
- Document IDs to add or remove

### Processing

1. Validate release is in "draft" status.
2. For additions: capture current commit SHA of each document.
3. For removals: remove document from changes array.
4. Update release document.
5. Create audit log entry.

### Outputs

- Updated release document
- Audit log entry for modification

### Error Handling

- Release not in draft status: reject with "Only draft releases can be edited"
- Document not found: reject with "Document not found"

## Acceptance Criteria

1. **Given** a draft release, **when** documents are added, **then** they appear in the release with their current commit SHA.
2. **Given** a draft release, **when** documents are removed, **then** they no longer appear in the release.
3. **Given** a release in "in_review" status, **when** an edit is attempted, **then** the edit is rejected with an error.
4. **Given** any draft modification, **when** completed, **then** an audit log entry is created.

## Traceability

| Direction | Link Type    | Target ID | Title                            |
| --------- | ------------ | --------- | -------------------------------- |
| Up        | derives-from | PRS-011    | Release creation & configuration |

## Source

**STORY-502 — Edit Draft Release**

> **As a** Quality Manager **I want** to add or remove documents from a draft release **So that** I can adjust the scope before submitting for review.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
