---
id: SRS-204
title: 'GitHub Webhook Handler'
status: draft
links:
  - type: derives-from
    target: PRS-005
---

# GitHub Webhook Handler

## Requirement

> **SRS-204.1:** The system **SHALL** receive GitHub webhook POST requests at a dedicated HTTP endpoint and validate the webhook signature using the `X-Hub-Signature-256` header and the GitHub App secret.
>
> **SRS-204.2:** The system **SHALL** trigger a document sync for changed markdown files (`.md`) when a push event is received on the default branch.
>
> **SRS-204.3:** The system **SHALL** process webhooks idempotently such that the same event processed multiple times produces the same result.
>
> **SRS-204.4:** The system **SHALL** queue sync jobs via Cloud Tasks for reliability and retry failed webhook processing.

## Context

The GitHub webhook handler is the `onGitHubWebhook` HTTP-triggered Cloud Function. It validates the `X-Hub-Signature-256` header against the GitHub App secret to ensure request authenticity. Push event payloads are parsed for added, modified, and removed files. Only markdown files in expected directories trigger document sync. Cloud Tasks provide reliable background processing with automatic retries.

## Classification

| Field           | Value                 |
| --------------- | --------------------- |
| Safety Class    | A                     |
| Category        | Interface             |
| Bounded Context | Repository Connection |

## Detailed Specification

### Inputs

- GitHub webhook POST request with JSON payload
- `X-Hub-Signature-256` header
- Push event payload containing changed files

### Processing

1. Validate webhook signature against GitHub App secret.
2. Parse push event payload for changed files (added, modified, removed).
3. Filter for markdown files (`.md`) in expected directories.
4. Queue sync jobs to Cloud Tasks queue for each affected repository.
5. Log webhook event for debugging.
6. Fire `GitHubWebhookReceived` domain event.

### Outputs

- HTTP 200 response to GitHub (acknowledgment)
- Sync jobs queued in Cloud Tasks
- Webhook event logged

### Error Handling

- Invalid signature: return HTTP 401 Unauthorized
- Unknown repository: log warning, return HTTP 200 (acknowledge but ignore)
- Cloud Tasks queue failure: return HTTP 500 (GitHub will retry)
- Malformed payload: return HTTP 400 Bad Request

## Acceptance Criteria

1. **Given** a valid push webhook from GitHub, **when** the endpoint receives it, **then** the signature is validated and a document sync is triggered for changed markdown files.
2. **Given** a webhook with an invalid `X-Hub-Signature-256`, **when** the endpoint processes it, **then** the request is rejected with HTTP 401.
3. **Given** the same webhook delivered twice, **when** both are processed, **then** the resulting state is identical (idempotent).
4. **Given** a push event that only modifies non-markdown files, **when** the webhook is processed, **then** no document sync is triggered.

## Traceability

| Direction | Link Type    | Target ID | Title                         |
| --------- | ------------ | --------- | ----------------------------- |
| Up        | derives-from | PRS-005    | Repository sync configuration |

## Source

**STORY-204 — GitHub Webhook Handler**

> **As the** PactoSigna system **I want** to receive and process GitHub webhooks **So that** document changes are synced automatically.

## Revision History

| Version | Date       | Author | Changes       |
| ------- | ---------- | ------ | ------------- |
| 0.1     | 2026-02-15 | AI     | Initial draft |
