---
id: SRS-201
title: 'Install GitHub App'
status: draft
links:
  - type: derives-from
    target: PRS-004
---

# Install GitHub App

## Requirement

> **SRS-201.1:** The system **SHALL** provide a "Connect GitHub" button in organization settings that redirects to the GitHub App installation flow.
>
> **SRS-201.2:** The system **SHALL** handle the GitHub App callback at `/api/github/callback`, storing the installation ID and GitHub organization name on the PactoSigna organization document.
>
> **SRS-201.3:** The system **SHALL** display a success message with the connected GitHub organization name after the installation flow completes.
>
> **SRS-201.4:** The system **SHALL** automatically configure webhooks via the GitHub App installation (no manual webhook setup required).

## Context

GitHub App installation is the entry point for repository integration. The pre-registered GitHub App requires `contents:read`, `metadata:read`, and `pull_requests:read` permissions. The installation flow is initiated via GitHub's standard URL (`https://github.com/apps/{app-name}/installations/new`). The `handleGitHubAppCallback` Cloud Function processes the callback and stores `gitHubAppInstallationId` and `gitHubOrgName` on the organization document.

## Classification

| Field           | Value                 |
| --------------- | --------------------- |
| Safety Class    | A                     |
| Category        | Interface             |
| Bounded Context | Repository Connection |

## Detailed Specification

### Inputs

- GitHub App installation callback parameters (installation_id, setup_action)
- User authentication (must be org admin)

### Processing

1. Admin clicks "Connect GitHub" button.
2. User is redirected to GitHub App installation page.
3. User selects GitHub organization and repositories.
4. GitHub redirects to callback URL with installation parameters.
5. Cloud Function validates the callback and stores installation data.
6. Webhooks are automatically configured by GitHub.

### Outputs

- `gitHubAppInstallationId` stored on organization document
- `gitHubOrgName` stored on organization document
- Success message displayed to user
- Webhooks configured for push and pull_request events

### Error Handling

- User cancels GitHub installation: redirect back to settings with info message
- Invalid callback parameters: log error, display "GitHub connection failed"
- GitHub API error: display error and suggest retry

## Acceptance Criteria

1. **Given** an admin on organization settings, **when** they click "Connect GitHub", **then** they are redirected to the GitHub App installation page.
2. **Given** a user completing the GitHub App installation, **when** GitHub redirects back, **then** the installation ID and GitHub org name are stored on the organization.
3. **Given** successful installation, **when** the callback completes, **then** a success message showing the connected GitHub organization name is displayed.
4. **Given** a completed installation, **when** checking the GitHub App configuration, **then** webhooks for push events are automatically configured.

## Traceability

| Direction | Link Type    | Target ID | Title                  |
| --------- | ------------ | --------- | ---------------------- |
| Up        | derives-from | PRS-004    | GitHub App integration |

## Source

**STORY-201 — Install GitHub App**

> **As an** organization admin **I want** to install the PactoSigna GitHub App on my GitHub organization **So that** PactoSigna can access my QMS repositories.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
