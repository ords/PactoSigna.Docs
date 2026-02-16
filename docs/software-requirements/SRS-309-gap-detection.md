---
id: SRS-309
title: 'Gap Detection'
status: draft
links:
  - type: derives-from
    target: PRS-008
---

# Gap Detection

## Requirement

> **SRS-309.1:** The system **SHALL** generate a gap report identifying documents with missing expected traceability links: User Needs without Requirements, Requirements without Tests, Requirements without upstream User Needs, and Architecture without implementing Requirements.
>
> **SRS-309.2:** The system **SHALL** allow filtering gap results by repository, document type, and status.
>
> **SRS-309.3:** The system **SHALL** display a gap count in the dashboard or navigation for visibility.
>
> **SRS-309.4:** The system **SHALL** support exporting the gap list.

## Context

Gap detection is critical for audit readiness. The `calculateGaps` Cloud Function performs the analysis on-demand or on a schedule. Gap counts are cached on the repository document for dashboard display. The analysis uses NOT IN logic against the links collection to find documents without expected link types.

## Classification

| Field           | Value               |
| --------------- | ------------------- |
| Safety Class    | B                   |
| Category        | Functional          |
| Bounded Context | Document Management |

## Detailed Specification

### Inputs

- Organization ID
- Optional filters: repository, document type, status

### Processing

1. For each gap rule (e.g., Requirements → Tests), query all source documents.
2. Check links collection for matching target links.
3. Identify sources without targets as gaps.
4. Store gap count on repository document.
5. Return gap report.

### Outputs

- Gap report listing all documents with missing links
- Gap count stored on repository for dashboard
- Exportable gap list

### Error Handling

- No documents to analyze: display "No documents found"
- Large dataset: process in batches

## Acceptance Criteria

1. **Given** a requirement without any linked test, **when** the gap report is generated, **then** that requirement appears in the "Requirements without Tests" gap list.
2. **Given** gap results, **when** a user filters by document type "requirement", **then** only requirement gaps are shown.
3. **Given** 3 gap items detected, **when** viewing the dashboard, **then** the gap count "3" is displayed.
4. **Given** a user clicking export, **when** the export runs, **then** the gap list is downloaded.

## Traceability

| Direction | Link Type    | Target ID | Title                           |
| --------- | ------------ | --------- | ------------------------------- |
| Up        | derives-from | PRS-008    | Document linking & traceability |

## Source

**STORY-309 — Gap Detection**

> **As a** QARA professional **I want** to find documents with missing traceability links **So that** I can ensure compliance before audits.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
