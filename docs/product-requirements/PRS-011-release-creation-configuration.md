---
id: PRS-011
title: 'Release Creation & Configuration'
status: draft
links:
  - type: derives-from
    target: UN-005
---

# Release Creation & Configuration

## Requirement Statements

1. The system **SHALL** allow authorized users (Owners and Admins) to create a release bundle by providing a name, version identifier, and optional description.

2. The system **SHALL** support three release types: QMS (documents from QMS repository only), Device (documents from a single device repository), and Combined (documents across QMS and one or more device repositories).

3. The system **SHALL** allow the release creator to select specific documents to include from any connected repository, locking each selected document to its current commit SHA at the time of inclusion.

4. The system **SHALL** allow editing of draft releases — adding or removing documents, updating name, version, or description — while preventing modification of non-draft releases.

5. The system **SHALL** automatically calculate required reviewer departments based on the types of documents included in the release and the organization's configured reviewer assignment rules.

6. The system **SHALL** display the calculated list of required signer departments and roles before the release is submitted for review.

## Rationale

ISO 13485 §4.2.4 and §7.3.7 require controlled change processes for QMS and design documents. The release bundle is the formal change control mechanism — it groups related changes, locks document versions to specific commits (ensuring reviewers sign a known state), and calculates required reviewers based on organizational rules. This ensures that every change goes through the appropriate departmental review before becoming effective.

## Classification

| Field    | Value                    |
| -------- | ------------------------ |
| Priority | Essential                |
| Category | Functional / Regulatory  |
| Standard | ISO 13485 §4.2.4, §7.3.7 |

## Acceptance Criteria

1. Given an Admin user, when they create a release with name "QMS-2026-Q1" and select 5 SOP documents, then a draft release is created with those documents locked to their current commit SHAs
2. Given a draft release containing SOP and Architecture documents, when the reviewer calculation runs, then the required departments include Quality (for SOPs) and Architecture + Security (for Architecture documents) per the configured reviewer rules
3. Given a draft release, when an Admin adds a new document to the release, then the document is locked to its current commit SHA and the required reviewer departments are recalculated
4. Given a release in "in_review" status, when an Admin attempts to add or remove documents, then the operation is rejected with an error indicating the release is no longer editable
5. Given a release with calculated reviewer departments, when the release detail view is loaded, then the required signer departments and their signing status are clearly displayed

## Traceability

| Direction | Link Type    | Target ID | Title                       |
| --------- | ------------ | --------- | --------------------------- |
| Up        | derives-from | UN-005    | Release Management          |
| Down      | refined-by   | —         | _(To be linked by SWR-xxx)_ |
| Down      | verified-by  | —         | _(To be linked by TP-xxx)_  |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
