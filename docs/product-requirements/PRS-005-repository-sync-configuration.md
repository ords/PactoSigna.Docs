---
id: PRS-005
title: 'Repository Sync Configuration'
status: draft
links:
  - type: derives-from
    target: UN-002
---

# Repository Sync Configuration

## Requirement Statements

1. The system **SHALL** allow organization administrators to select which GitHub repositories to connect and classify each as QMS (one per organization) or Device (multiple allowed).

2. The system **SHALL** require device repositories to capture a device name and IEC 62304 safety class (A, B, or C), where Class C devices require additional document types (Software Detailed Design).

3. The system **SHALL** trigger an initial full sync upon repository connection, indexing all markdown documents from the configured branch.

4. The system **SHALL** automatically sync changed files when pushes are made to connected repositories via GitHub webhook events.

5. The system **SHALL** allow administrators to trigger a manual re-sync of any connected repository at any time.

6. The system **SHALL** provide a sync status dashboard showing, per repository: last sync time, document count, current sync status (idle, in_progress, error), and any error details.

7. The system **SHALL** preserve historical sync data and document metadata when a repository is disconnected, while ceasing all future sync activity.

8. The system **SHALL** support configurable branch selection per repository (defaulting to the main branch).

## Rationale

Medical device organizations maintain separate repositories for their QMS (SOPs, policies) and for each device under development. IEC 62304 safety classification determines the required documentation rigor — Class C devices need Software Detailed Design documents that Class A/B do not. The sync configuration ensures PactoSigna correctly indexes and classifies documents across all connected repositories, providing the foundation for traceability and release management.

## Classification

| Field    | Value                                  |
| -------- | -------------------------------------- |
| Priority | Essential                              |
| Category | Functional                             |
| Standard | IEC 62304 §4.3 (safety classification) |

## Acceptance Criteria

1. Given an Admin, when they connect a repository and classify it as QMS, then it becomes the organization's QMS repository (only one allowed)
2. Given an Admin connecting a device repository, when they do not provide a device name or safety class, then the system rejects the configuration with a validation error
3. Given a newly connected repository, when the initial sync completes, then all markdown files from the configured branch are indexed in Firestore with parsed frontmatter
4. Given a connected repository, when a push event is received via webhook, then only changed files are synced (incremental sync)
5. Given a sync in progress, when an error occurs, then the sync status shows the error details and the administrator can retry via manual re-sync
6. Given a disconnected repository, when querying historical documents, then previously synced document metadata remains accessible
7. Given a Class C device repository, when documents are synced, then the system expects and indexes Software Detailed Design documents from the `/detailed-design/` directory

## Traceability

| Direction | Link Type    | Target ID | Title                       |
| --------- | ------------ | --------- | --------------------------- |
| Up        | derives-from | UN-002    | Repository Connection       |
| Down      | refined-by   | —         | _(To be linked by SWR-xxx)_ |
| Down      | verified-by  | —         | _(To be linked by TP-xxx)_  |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
