---
id: PRS-008
title: 'Document Linking & Traceability'
status: draft
links:
  - type: derives-from
    target: UN-003
---

# Document Linking & Traceability

## Requirement Statements

1. The system **SHALL** support the following frontmatter link types: `derives-from`, `implements`, `verified-by`, `mitigates`, and `related-to`.

2. The system **SHALL** store each frontmatter link as a queryable Link document in Firestore with fields for source document ID, target document ID, link type, creation timestamp, and the sync event that created it.

3. The system **SHALL** maintain bidirectional link visibility — when Document A links to Document B, both documents display the relationship in their metadata panel (A shows downstream link, B shows upstream link).

4. The system **SHALL** update Link documents during sync: new links in frontmatter are created, removed links are soft-deleted, and changed links are versioned.

5. The system **SHALL** validate link targets during sync and flag broken links (references to non-existent document IDs) as warnings without blocking the sync.

6. The system **SHALL** provide an API for querying links by source document, target document, or link type for use by both the frontend and MCP servers.

## Rationale

IEC 62304 and FDA design control guidance require bidirectional traceability between user needs, requirements, architecture, tests, and risks. Frontmatter links are the mechanism by which engineers declare traceability relationships in their natural Git workflow. Storing links as first-class Firestore entities enables fast traceability queries, gap detection, and coverage matrix generation — the foundation for PRS-021 (Traceability Matrix) and PRS-020 (Risk-Requirement Traceability).

## Classification

| Field    | Value                                 |
| -------- | ------------------------------------- |
| Priority | Essential                             |
| Category | Functional / Regulatory               |
| Standard | IEC 62304 §5.1.1, FDA Design Controls |

## Acceptance Criteria

1. Given a document with `links: [{type: derives-from, target: UN-001}]` in frontmatter, when synced, then a Link document is created with linkType "derives-from", sourceDocId matching the document, and targetDocId "UN-001"
2. Given Document A links to Document B, when viewing Document B's detail page, then the metadata panel shows Document A as an upstream linked document with the correct link type
3. Given a link that existed in the previous sync but is removed from frontmatter, when re-synced, then the Link document is soft-deleted (marked as removed, not physically deleted)
4. Given a frontmatter link targeting a document ID that does not exist in Firestore, when synced, then the link is created but flagged with a broken-link warning in the sync event details
5. Given a query for all links where targetDocId is "REQ-001", then all documents linking to REQ-001 are returned with their link types and source document metadata

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
