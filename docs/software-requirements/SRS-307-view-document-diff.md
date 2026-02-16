---
id: SRS-307
title: 'View Document Diff'
status: draft
links:
  - type: derives-from
    target: PRS-008
---

# View Document Diff

## Requirement

> **SRS-307.1:** The system **SHALL** provide a diff view that shows added lines (green) and removed lines (red) between two document versions.
>
> **SRS-307.2:** The system **SHALL** allow users to select any two versions to compare, not limited to consecutive versions.
>
> **SRS-307.3:** The system **SHALL** include both content and frontmatter changes in the diff view, with syntax highlighting applied to the diff output.

## Context

Document diff enables team members to understand what changed between versions. The GitHub Compare API (`GET /repos/{owner}/{repo}/compare/{base}...{head}`) is used to fetch diffs. Libraries such as `diff2html` can be used for rendering.

## Classification

| Field           | Value               |
| --------------- | ------------------- |
| Safety Class    | A                   |
| Category        | Functional          |
| Bounded Context | Document Management |

## Detailed Specification

### Inputs

- Document ID
- Two version identifiers (commit SHAs) to compare

### Processing

1. User selects two versions from the document changelog.
2. Fetch diff from GitHub Compare API.
3. Parse and render diff with added/removed line highlighting.
4. Apply syntax highlighting to diff output.

### Outputs

- Visual diff showing added (green) and removed (red) lines
- Both content and frontmatter changes displayed

### Error Handling

- GitHub API failure: display "Unable to load diff. Please try again."
- Versions too far apart (GitHub API limit): display warning

## Acceptance Criteria

1. **Given** a member viewing a document, **when** they select two versions to compare, **then** a diff view shows added and removed lines with color coding.
2. **Given** two non-consecutive versions, **when** compared, **then** the diff correctly reflects all changes between them.
3. **Given** a diff that includes frontmatter changes, **when** displayed, **then** frontmatter changes are visible alongside content changes.

## Traceability

| Direction | Link Type    | Target ID | Title                           |
| --------- | ------------ | --------- | ------------------------------- |
| Up        | derives-from | PRS-008    | Document linking & traceability |

## Source

**STORY-307 — View Document Diff**

> **As a** member **I want** to see what changed between document versions **So that** I can understand the evolution of requirements.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
