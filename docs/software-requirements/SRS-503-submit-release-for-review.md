---
id: SRS-503
title: 'Submit Release for Review'
status: draft
links:
  - type: derives-from
    target: PRS-012
---

# Submit Release for Review

## Requirement

> **SRS-503.1:** The system **SHALL** allow Quality Managers to submit a draft release for review, changing its status to "in_review".
>
> **SRS-503.2:** The system **SHALL** calculate the required reviewer departments based on the document types included in the release and the organization's reviewer rules.
>
> **SRS-503.3:** The system **SHALL** record `submittedForReviewAt` and `submittedForReviewBy` timestamps upon submission.
>
> **SRS-503.4:** The system **SHALL** create an audit log entry for the submission.

## Context

Submitting a release for review initiates the formal approval process. Required departments are derived from `OrganizationSettings.reviewerRules` based on the document types included in the release. Future enhancement: email notifications to required reviewers.

## Classification

| Field           | Value              |
| --------------- | ------------------ |
| Safety Class    | B                  |
| Category        | Functional         |
| Bounded Context | Release Management |

## Detailed Specification

### Inputs

- Release ID (must be in draft status)

### Processing

1. Validate release is in "draft" status.
2. Determine document types in the release.
3. Calculate required departments from reviewer rules.
4. Update release status to "in_review".
5. Record `submittedForReviewAt` and `submittedForReviewBy`.
6. Create audit log entry.

### Outputs

- Release status changed to "in_review"
- Required departments set on the release
- Submission timestamps recorded
- Audit log entry

### Error Handling

- Release not in draft status: reject with "Only draft releases can be submitted"
- No documents in release: reject with "Release must contain at least one document"
- No matching reviewer rules: warn but allow submission

## Acceptance Criteria

1. **Given** a draft release with documents, **when** submitted for review, **then** the status changes to "in_review".
2. **Given** a release containing requirement and architecture documents, **when** submitted, **then** the required departments are calculated based on reviewer rules.
3. **Given** a submitted release, **when** examining the record, **then** `submittedForReviewAt` and `submittedForReviewBy` are populated.
4. **Given** a release submission, **when** completed, **then** an audit log entry with action `release.submitted_for_review` is created.

## Traceability

| Direction | Link Type    | Target ID | Title            |
| --------- | ------------ | --------- | ---------------- |
| Up        | derives-from | PRS-012    | Release workflow |

## Source

**STORY-503 — Submit Release for Review**

> **As a** Quality Manager **I want** to submit a release for departmental review **So that** the required reviewers can sign off.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
