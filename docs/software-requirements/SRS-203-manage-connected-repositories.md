---
id: SRS-203
title: 'Manage Connected Repositories'
status: draft
links:
  - type: derives-from
    target: PRS-004
---

# Manage Connected Repositories

## Requirement

> **SRS-203.1:** The system **SHALL** display a repository list showing name, type, device name (if applicable), sync status, and last synced timestamp for each connected repository.
>
> **SRS-203.2:** The system **SHALL** allow admins to update repository settings (type, device name, safety class) and to disconnect repositories.
>
> **SRS-203.3:** The system **SHALL** preserve historical data when a repository is disconnected (set sync status to 'paused', do not delete documents).
>
> **SRS-203.4:** The system **SHALL** allow admins to trigger a manual re-sync for any connected repository.

## Context

Repository management provides ongoing control over connected GitHub repositories. Disconnecting a repository pauses sync and preserves data. The `updateRepository`, `disconnectRepository`, and `triggerRepositorySync` Cloud Functions handle mutations. The `RepositoryDisconnected` domain event is fired on disconnection. Sync status values include: active, syncing, error.

## Classification

| Field           | Value                 |
| --------------- | --------------------- |
| Safety Class    | A                     |
| Category        | Functional            |
| Bounded Context | Repository Connection |

## Detailed Specification

### Inputs

- Repository ID (for update, disconnect, or re-sync)
- Updated settings (type, device name, safety class)

### Processing

1. Display all connected repositories with current status.
2. For updates: validate and apply changes via Cloud Function.
3. For disconnection: set `syncStatus: 'paused'`, retain all documents.
4. For manual sync: queue sync task.
5. Create audit log entries for all actions.

### Outputs

- Updated repository documents in Firestore
- Audit log entries
- Sync triggered (for manual re-sync)

### Error Handling

- Repository not found: return 404
- Sync already in progress: display "Sync is already running"
- GitHub API unavailable: display error in sync status

## Acceptance Criteria

1. **Given** an admin on the repository settings page, **when** they view the list, **then** all connected repositories are shown with name, type, sync status, and last synced.
2. **Given** an admin disconnecting a repository, **when** they confirm disconnection, **then** the sync status changes to 'paused' and historical data is preserved.
3. **Given** an admin, **when** they trigger a manual re-sync, **then** a sync process is initiated for the selected repository.
4. **Given** a repository with a sync error, **when** the admin views the repository list, **then** the error status and message are displayed.

## Traceability

| Direction | Link Type    | Target ID | Title                  |
| --------- | ------------ | --------- | ---------------------- |
| Up        | derives-from | PRS-004    | GitHub App integration |

## Source

**STORY-203 — Manage Connected Repositories**

> **As an** organization admin **I want** to manage my connected repositories **So that** I can add new repos or disconnect old ones.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
