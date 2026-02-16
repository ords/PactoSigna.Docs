---
id: UN-003
title: 'Document Management & Sync'
status: draft
links:
  - type: derives-from
    target: US-UP-001
  - type: derives-from
    target: US-UP-002
  - type: derives-from
    target: epic-03-document-management
---

# Document Management & Sync

## Need Statement

> As a **QA Manager**, I need **to browse, search, and view all QMS and device documents synced from GitHub — with rendered markdown, metadata panels, version history, and linked items — ** so that **I can review documentation quality, verify completeness, and prepare for audits without switching between GitHub and multiple tools**.

> As a **Developer User**, I need **documents I author as markdown in GitHub to appear in PactoSigna with correct type classification, parsed frontmatter, and automatic change tracking** so that **my workflow stays in Git while the QMS stays up to date**.

## Context

Document management is the backbone of PactoSigna. The system must parse YAML frontmatter from markdown files, classify documents by type (SOP, Work Instruction, Policy, User Need, Requirement, Architecture, Detailed Design, Test, Risk), store metadata in Firestore, and present documents through a rich UI with markdown rendering and Mermaid diagram support.

This need spans the entire lifecycle: initial sync indexes documents; ongoing syncs track changes via changelog entries (commit SHA, author, change type); the UI provides browsing with filters (type, status, repository), search, and detail views with metadata panels showing linked items. The device switcher allows focusing on a specific repository context.

Document type is inferred from the file's directory path if not explicitly set in frontmatter, ensuring a low-friction authoring experience. Frontmatter links (derives-from, implements, verified-by, mitigates, etc.) are parsed and stored in Firestore for fast traceability queries.

## Stakeholder

| Field   | Value                                                         |
| ------- | ------------------------------------------------------------- |
| Persona | QA Manager (Sarah Chen), Developer User (Alex Park)           |
| Source  | Epic 03 — Document Management; Product Vision §Document Model |

## Acceptance Criteria

1. Webhook-triggered sync parses YAML frontmatter and stores document metadata (id, title, status, docType, links) in Firestore
2. Document type is correctly inferred from directory path when not specified in frontmatter
3. Each sync creates immutable changelog entries recording commit SHA, change type, author, and date
4. Frontmatter `links` arrays are extracted and stored as queryable Link documents in Firestore
5. Document list view supports filtering by type, status, and repository; searching by title or ID; and sorting by multiple fields
6. Document detail view renders markdown with syntax highlighting and Mermaid diagrams inline
7. Document detail view shows a metadata panel with frontmatter fields, linked documents (upstream and downstream), and version history
8. Users can view diffs between any two document versions
9. Device switcher in the header allows focusing all views on a specific repository (QMS or device)
10. Deleted files are marked as removed in Firestore while preserving history

## Traceability

| Direction | Link Type      | Target ID                   | Title                       |
| --------- | -------------- | --------------------------- | --------------------------- |
| Up        | derives-from   | US-UP-001                   | QA Manager Persona          |
| Up        | derives-from   | US-UP-002                   | Developer User Persona      |
| Down      | implemented-by | epic-03-document-management | Epic 3: Document Management |
| Down      | implemented-by | STORY-301                   | Sync Document Metadata      |
| Down      | implemented-by | STORY-302                   | Document Type Inference     |
| Down      | implemented-by | STORY-303                   | Track Document Changelog    |
| Down      | implemented-by | STORY-304                   | Extract and Store Links     |
| Down      | implemented-by | STORY-305                   | Document List View          |
| Down      | implemented-by | STORY-306                   | Document Detail View        |
| Down      | implemented-by | STORY-307                   | View Document Diff          |
| Down      | implemented-by | STORY-310                   | Device Switcher             |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
