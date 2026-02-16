---
id: SRS-202
title: 'Select Repositories'
status: draft
links:
  - type: derives-from
    target: PRS-004
---

# Select Repositories

## Requirement

> **SRS-202.1:** The system **SHALL** display a list of accessible repositories from the GitHub App installation and allow admins to toggle the "Connect" status for each.
>
> **SRS-202.2:** The system **SHALL** require admins to specify a repository type (QMS Repository or Device Repository) for each connected repository, enforcing a limit of one QMS Repository per organization.
>
> **SRS-202.3:** The system **SHALL** require device name and safety class (A, B, or C) for repositories designated as Device Repositories.
>
> **SRS-202.4:** The system **SHALL** trigger an initial document sync for each newly connected repository via the `connectRepository` Cloud Function.

## Context

After GitHub App installation, admins select which repositories to sync. The GitHub API (`GET /installation/repositories`) provides the list of accessible repos. Connected repositories are stored in `/organizations/{orgId}/repositories/{repoId}`. The `RepositoryConnected` domain event is fired for each new connection.

## Classification

| Field           | Value                 |
| --------------- | --------------------- |
| Safety Class    | A                     |
| Category        | Functional            |
| Bounded Context | Repository Connection |

## Detailed Specification

### Inputs

- Repository selection (toggle connect/disconnect)
- Repository type (QMS or Device)
- Device name (for Device repositories)
- Safety class (A / B / C, for Device repositories)

### Processing

1. Fetch accessible repositories from GitHub API.
2. Display repositories with connect toggle.
3. For each connected repository, validate type and required fields.
4. Create repository document in Firestore via Cloud Function.
5. Trigger initial sync for each connected repository.
6. Fire `RepositoryConnected` domain event.

### Outputs

- Repository documents in Firestore
- Initial sync triggered for each connected repository
- Audit log entries for each connection

### Error Handling

- Attempting to connect a second QMS repository: reject with "Only one QMS repository is allowed per organization"
- Missing device name or safety class for Device repo: display validation error
- GitHub API failure: display error and allow retry

## Acceptance Criteria

1. **Given** a completed GitHub App installation, **when** the admin views the repository selection screen, **then** all accessible repositories are listed.
2. **Given** a repository connected as type QMS, **when** another repository is set to QMS type, **then** the system rejects it with an error.
3. **Given** a repository connected as Device type, **when** the admin does not provide a device name and safety class, **then** validation prevents saving.
4. **Given** a newly connected repository, **when** the connection is saved, **then** an initial document sync is automatically triggered.

## Traceability

| Direction | Link Type    | Target ID | Title                  |
| --------- | ------------ | --------- | ---------------------- |
| Up        | derives-from | PRS-004    | GitHub App integration |

## Source

**STORY-202 — Select Repositories**

> **As an** organization admin **I want** to select which repositories to sync with PactoSigna **So that** only relevant QMS repositories are indexed.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
