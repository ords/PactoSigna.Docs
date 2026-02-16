---
id: SRS-505
title: 'List Releases'
status: draft
links:
  - type: derives-from
    target: PRS-012
---

# List Releases

## Requirement

> **SRS-505.1:** The system **SHALL** display a list of all releases for the organization, sorted by creation date with newest first.
>
> **SRS-505.2:** The system **SHALL** support filtering by status (draft, in_review, approved, published) and by release type (qms, device, combined).
>
> **SRS-505.3:** The system **SHALL** support searching releases by name or version.

## Context

The release list page provides an overview of all releases and their progress. Filtering by status helps users focus on specific workflow stages (e.g., in-review releases needing signatures).

## Classification

| Field           | Value              |
| --------------- | ------------------ |
| Safety Class    | A                  |
| Category        | Functional         |
| Bounded Context | Release Management |

## Detailed Specification

### Inputs

- Filter criteria: status, release type
- Search text: name or version

### Processing

1. Query Firestore releases collection for the organization.
2. Apply filters (status, type).
3. Apply search filter on name and version.
4. Sort by creation date descending.

### Outputs

- List of releases with summary information
- Filter/search controls

### Error Handling

- No releases found: display "No releases found"
- Query error: display error with retry option

## Acceptance Criteria

1. **Given** a team member viewing the releases page, **when** releases exist, **then** they are listed sorted by creation date (newest first).
2. **Given** filtering by status "in_review", **when** the filter is applied, **then** only releases in review are shown.
3. **Given** filtering by type "device", **when** the filter is applied, **then** only device releases are shown.
4. **Given** a search for "v2.0", **when** searching, **then** releases with matching name or version are displayed.

## Traceability

| Direction | Link Type    | Target ID | Title            |
| --------- | ------------ | --------- | ---------------- |
| Up        | derives-from | PRS-012    | Release workflow |

## Source

**STORY-505 — List Releases**

> **As a** Team Member **I want** to see all releases for my organization **So that** I can find and track release progress.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
