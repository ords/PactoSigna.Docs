---
id: SRS-205
title: 'Sync Status Dashboard'
status: draft
links:
  - type: derives-from
    target: PRS-005
---

# Sync Status Dashboard

## Requirement

> **SRS-205.1:** The system **SHALL** display a sync status dashboard showing all connected repositories with their last successful sync time, number of documents indexed, current sync status (idle / syncing / error), and error message (if applicable).
>
> **SRS-205.2:** The system **SHALL** provide real-time status updates via Firestore listeners (`onSnapshot`) so the dashboard reflects changes without manual refresh.
>
> **SRS-205.3:** The system **SHALL** allow admins to view recent sync events for each repository and trigger a manual sync from the dashboard.

## Context

The sync status dashboard provides visibility into the health of repository connections. Repository documents contain `syncStatus`, `lastSyncedAt`, and `lastSyncError` fields. Sync events are logged to `/organizations/{orgId}/repositories/{repoId}/syncEvents`. Real-time updates use Firestore `onSnapshot` listeners for immediate feedback.

## Classification

| Field           | Value                 |
| --------------- | --------------------- |
| Safety Class    | A                     |
| Category        | Functional            |
| Bounded Context | Repository Connection |

## Detailed Specification

### Inputs

- Organization ID (from current context)
- Repository ID (for drill-down view)

### Processing

1. Query all connected repositories for the organization.
2. Subscribe to real-time updates via Firestore `onSnapshot`.
3. Display aggregate status for each repository.
4. For drill-down: query recent sync events subcollection.

### Outputs

- Dashboard view with repository sync statuses
- Real-time updates as sync status changes
- Sync event history per repository

### Error Handling

- Firestore listener disconnected: display "Connection lost, reconnecting..."
- No repositories connected: display prompt to connect a repository

## Acceptance Criteria

1. **Given** an admin on the sync dashboard, **when** repositories are connected, **then** each repository shows last sync time, document count, and status.
2. **Given** a sync in progress, **when** the status changes, **then** the dashboard updates in real-time without manual refresh.
3. **Given** a repository with a sync error, **when** viewing the dashboard, **then** the error status and message are clearly displayed.
4. **Given** an admin clicking a repository, **when** the detail view opens, **then** recent sync events are displayed.

## Traceability

| Direction | Link Type    | Target ID | Title                         |
| --------- | ------------ | --------- | ----------------------------- |
| Up        | derives-from | PRS-005    | Repository sync configuration |

## Source

**STORY-205 — Sync Status Dashboard**

> **As an** organization admin **I want** to see the sync status of my repositories **So that** I know if documents are up to date.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
