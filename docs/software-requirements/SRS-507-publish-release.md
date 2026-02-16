---
id: SRS-507
title: 'Publish Release'
status: draft
links:
  - type: derives-from
    target: PRS-013
---

# Publish Release

## Requirement

> **SRS-507.1:** The system **SHALL** allow Quality Managers to publish an approved release, changing its status to "published".
>
> **SRS-507.2:** The system **SHALL** make published releases immutable (no further modifications allowed).
>
> **SRS-507.3:** The system **SHALL** record `publishedAt` and `publishedBy` timestamps upon publication.
>
> **SRS-507.4:** The system **SHALL** create an audit log entry for the publication action.

## Context

Publishing a release finalizes it as the official version. Once published, the release and its associated documents are immutable—forming the regulatory record of that release. The `publishedAt` and `publishedBy` fields capture the provenance of the publication.

## Classification

| Field           | Value              |
| --------------- | ------------------ |
| Safety Class    | B                  |
| Category        | Functional         |
| Bounded Context | Release Management |

## Detailed Specification

### Inputs

- Release ID (must be in "approved" status)

### Processing

1. Validate release is in "approved" status.
2. Change release status to "published".
3. Record `publishedAt` and `publishedBy`.
4. Mark release as immutable.
5. Create audit log entry.

### Outputs

- Release status changed to "published"
- `publishedAt` and `publishedBy` recorded
- Release becomes immutable
- Audit log entry

### Error Handling

- Release not in "approved" status: reject with "Only approved releases can be published"
- Attempt to modify published release: reject with "Published releases are immutable"

## Acceptance Criteria

1. **Given** an approved release, **when** a Quality Manager publishes it, **then** the status changes to "published".
2. **Given** a published release, **when** any modification is attempted, **then** the modification is rejected.
3. **Given** a successful publication, **when** examining the release, **then** `publishedAt` and `publishedBy` are populated.
4. **Given** publication, **when** completed, **then** an audit log entry with action `release.published` is created.

## Traceability

| Direction | Link Type    | Target ID | Title                       |
| --------- | ------------ | --------- | --------------------------- |
| Up        | derives-from | PRS-013    | Release artifact generation |

## Source

**STORY-507 — Publish Release**

> **As a** Quality Manager **I want** to publish an approved release **So that** it becomes the official version.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
