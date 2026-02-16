---
id: SRS-206
title: 'Handle GitHub App Uninstallation'
status: draft
links:
  - type: derives-from
    target: PRS-005
---

# Handle GitHub App Uninstallation

## Requirement

> **SRS-206.1:** The system **SHALL** process the `installation.deleted` webhook event when the GitHub App is uninstalled from a GitHub organization.
>
> **SRS-206.2:** The system **SHALL** mark all repositories associated with the uninstalled GitHub App installation as disconnected and set their sync status to 'disconnected'.
>
> **SRS-206.3:** The system **SHALL** preserve all historical document data for disconnected repositories (no data deletion).
>
> **SRS-206.4:** The system **SHALL** notify the organization admin (email or in-app notification) when the GitHub App is uninstalled.

## Context

GitHub App uninstallation is handled by the `handleGitHubAppUninstalled` Cloud Function, triggered by the `installation.deleted` webhook event. All repositories matching the installation ID are updated to a disconnected state. Historical data is preserved to maintain audit trail integrity. Admins can later reinstall the GitHub App to reconnect.

## Classification

| Field           | Value                 |
| --------------- | --------------------- |
| Safety Class    | A                     |
| Category        | Interface             |
| Bounded Context | Repository Connection |

## Detailed Specification

### Inputs

- `installation.deleted` webhook event from GitHub
- Installation ID from the event payload

### Processing

1. Validate webhook signature.
2. Identify all repositories with matching installation ID.
3. Update all matching repositories: set sync status to 'disconnected'.
4. Send notification to organization admins.
5. Create audit log entries.

### Outputs

- All affected repositories marked as disconnected
- Admin notification (email or in-app)
- Audit log entries

### Error Handling

- Unknown installation ID: log warning, return HTTP 200
- Notification sending failure: log error, continue processing (data update takes priority)

## Acceptance Criteria

1. **Given** a GitHub App uninstallation, **when** the `installation.deleted` webhook is received, **then** all repositories for that installation are marked as disconnected.
2. **Given** disconnected repositories, **when** viewing the repository list, **then** the sync status shows "disconnected".
3. **Given** a GitHub App uninstallation, **when** processing completes, **then** all historical document data is preserved.
4. **Given** a GitHub App uninstallation, **when** the event is processed, **then** the organization admin is notified.

## Traceability

| Direction | Link Type    | Target ID | Title                         |
| --------- | ------------ | --------- | ----------------------------- |
| Up        | derives-from | PRS-005    | Repository sync configuration |

## Source

**STORY-206 — Handle GitHub App Uninstallation**

> **As the** PactoSigna system **I want** to handle GitHub App uninstallation gracefully **So that** users understand when access is revoked.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
