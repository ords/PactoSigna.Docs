# Architecture Decision Records

This directory contains Architecture Decision Records (ADRs) for PactoSigna.

## Index

| ADR                                                    | Title                                        | Status   | Date       |
| ------------------------------------------------------ | -------------------------------------------- | -------- | ---------- |
| [ADR-001](adr-001-foundation-layer-priority.md)        | Foundation Layer Priority                    | Accepted | 2026-01-13 |
| [ADR-002](adr-002-github-app-integration.md)           | GitHub App Integration                       | Accepted | 2026-01-13 |
| [ADR-003](adr-003-self-service-multi-tenancy.md)       | Self-Service Multi-Tenancy                   | Accepted | 2026-01-13 |
| [ADR-004](adr-004-email-password-authentication.md)    | Email/Password Authentication with SSO Path  | Accepted | 2026-01-13 |
| [ADR-005](adr-005-department-based-permissions.md)     | Department-Based Permissions                 | Accepted | 2026-01-13 |
| [ADR-006](adr-006-metadata-only-document-sync.md)      | Metadata-Only Document Sync                  | Accepted | 2026-01-13 |
| [ADR-007](adr-007-hybrid-signature-flow.md)            | Hybrid Signature Flow                        | Accepted | 2026-01-13 |
| [ADR-008](adr-008-backend-only-firestore-writes.md)    | Backend-Only Data Access                     | Accepted | 2026-01-13 |
| [ADR-009](adr-009-document-changelog-subcollection.md) | Document Changelog Subcollection             | Accepted | 2026-01-13 |
| [ADR-011](adr-011-rest-api-migration.md)               | Bounded Context REST API Architecture        | Accepted | 2026-01-16 |
| [ADR-012](adr-012-playwright-bdd-testing.md)           | Playwright BDD Testing Architecture          | Accepted | 2026-01-17 |
| [ADR-013](adr-013-file-naming-conventions.md)          | File Naming Conventions                      | Accepted | 2026-01-27 |
| [ADR-014](adr-014-shared-package-purpose.md)           | Shared Package Purpose                       | Accepted | 2026-01-30 |
| [ADR-015](adr-015-issue-driven-ai-workflow.md)         | Issue-Driven AI Development Workflow         | Accepted | 2026-02-01 |
| [ADR-016](adr-016-external-service-abstraction.md)     | External Service Abstraction for Testability | Accepted | 2026-02-02 |
| [ADR-017](adr-017-journey-based-e2e-testing.md)        | Journey-Based E2E Testing                    | Accepted | 2026-02-03 |
| [ADR-018](adr-018-server-side-compliance-data.md)      | Server-Side Compliance Data                  | Accepted | 2026-02-04 |
| [ADR-019](adr-019-entity-reference-validation.md)      | Entity Reference Validation                  | Accepted | 2026-02-04 |
| [ADR-020](adr-020-viewmodel-hook-pattern.md)           | ViewModel Hook Pattern                       | Accepted | 2026-02-06 |
| [ADR-021](adr-021-app-shell-loading-protocol.md)       | App Shell Loading Protocol                   | Accepted | 2026-02-06 |

## ADR Template

When creating new ADRs, use the following template:

```markdown
# ADR-XXX: Title

## Status

Proposed | Accepted | Deprecated | Superseded

## Context

What is the issue that we're seeing that is motivating this decision?

## Decision

What is the change that we're proposing and/or doing?

## Consequences

What becomes easier or more difficult to do because of this change?
```
