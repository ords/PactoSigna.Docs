---
id: PRS-007
title: 'Document Lifecycle Management'
status: draft
links:
  - type: derives-from
    target: UN-003
---

# Document Lifecycle Management

## Requirement Statements

1. The system **SHALL** support document status values of at minimum: draft, effective, and obsolete, as specified in frontmatter metadata.

2. The system **SHALL** display documents in a list view that supports filtering by document type, status, and repository, with search by title or document ID.

3. The system **SHALL** render document content as formatted markdown with syntax highlighting and inline Mermaid diagram rendering.

4. The system **SHALL** display a metadata panel on the document detail view showing frontmatter fields, linked documents (upstream and downstream), and version history.

5. The system **SHALL** allow users to view diffs between any two versions of a document, showing content changes between commit SHAs.

6. The system **SHALL** provide a device/repository switcher that focuses all document views on a specific context (QMS or a specific device repository).

7. The system **SHALL** support sorting the document list by multiple fields (title, document type, status, last modified date).

## Rationale

ISO 13485 §4.2.4 requires controlled documents to be identifiable by status, with procedures ensuring current versions are available at points of use and obsolete documents are prevented from unintended use. The document lifecycle views give QA Managers and Regulatory Specialists visibility into the full document corpus — browsing, searching, filtering, and inspecting documents without switching to GitHub. The diff view enables precise change tracking for audit preparation.

## Classification

| Field    | Value                                   |
| -------- | --------------------------------------- |
| Priority | Essential                               |
| Category | Functional                              |
| Standard | ISO 13485 §4.2.4 (control of documents) |

## Acceptance Criteria

1. Given a document with `status: effective` in frontmatter, when viewed in the document list, then it displays the "Effective" status badge
2. Given a user filtering by document type "SOP" and status "effective", when the filter is applied, then only SOP documents with effective status are shown
3. Given a document with Mermaid code blocks, when rendered in the detail view, then the Mermaid diagrams are displayed as rendered SVG graphics inline
4. Given a document with frontmatter links, when viewed in the detail view, then linked documents are displayed as clickable items in the metadata panel with link type labels
5. Given two commit SHAs for a document, when a user requests a diff, then the content differences between those versions are displayed with additions and deletions highlighted
6. Given the device switcher set to "CardioMonitor", when viewing the document list, then only documents from the CardioMonitor repository are shown

## Traceability

| Direction | Link Type    | Target ID | Title                       |
| --------- | ------------ | --------- | --------------------------- |
| Up        | derives-from | UN-003    | Document Management & Sync  |
| Down      | refined-by   | —         | _(To be linked by SWR-xxx)_ |
| Down      | verified-by  | —         | _(To be linked by TP-xxx)_  |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
