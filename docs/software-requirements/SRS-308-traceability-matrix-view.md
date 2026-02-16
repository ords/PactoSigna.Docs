---
id: SRS-308
title: 'Traceability Matrix View'
status: draft
links:
  - type: derives-from
    target: PRS-008
---

# Traceability Matrix View

## Requirement

> **SRS-308.1:** The system **SHALL** display a traceability matrix that allows users to select a source document type and a target document type, showing all source documents as rows and their linked target documents.
>
> **SRS-308.2:** The system **SHALL** highlight gaps (source documents without linked targets) and calculate a coverage percentage.
>
> **SRS-308.3:** The system **SHALL** allow filtering by document status and support exporting the matrix as CSV.
>
> **SRS-308.4:** The system **SHALL** allow users to click any document in the matrix to navigate to its detail view.

## Context

The traceability matrix is a key compliance artifact that demonstrates coverage between document types (e.g., Requirements → Tests). Coverage percentage helps identify gaps before audits. The matrix is built by querying the links collection grouped by source document.

## Classification

| Field           | Value               |
| --------------- | ------------------- |
| Safety Class    | B                   |
| Category        | Functional          |
| Bounded Context | Document Management |

## Detailed Specification

### Inputs

- Source document type (e.g., Requirements)
- Target document type (e.g., Tests)
- Optional status filter

### Processing

1. Query links collection grouped by source document type.
2. Join with documents collection for display metadata.
3. Calculate coverage: (sources with at least one linked target) / (total sources).
4. Identify gaps (sources without targets).

### Outputs

- Matrix view with source documents as rows and linked targets
- Gap highlighting
- Coverage percentage
- CSV export

### Error Handling

- No documents of selected type: display "No documents found for the selected type"
- Large dataset: paginate results

## Acceptance Criteria

1. **Given** a user selecting Requirements as source and Tests as target, **when** the matrix loads, **then** all requirements are shown with their linked tests.
2. **Given** a requirement with no linked tests, **when** the matrix displays, **then** the requirement is highlighted as a gap.
3. **Given** a matrix with 3 of 4 requirements having tests, **when** viewing the matrix, **then** the coverage shows "75%".
4. **Given** a user clicking "Export CSV", **when** the export runs, **then** a CSV file containing the matrix data is downloaded.

## Traceability

| Direction | Link Type    | Target ID | Title                           |
| --------- | ------------ | --------- | ------------------------------- |
| Up        | derives-from | PRS-008    | Document linking & traceability |

## Source

**STORY-308 — Traceability Matrix View**

> **As a** member **I want** to see a traceability matrix between document types **So that** I can verify coverage and find gaps.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
