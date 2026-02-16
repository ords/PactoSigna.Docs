---
id: SOUP-001
title: 'SOUP Dependency List (Runtime)'
status: draft
links:
  - type: related-to
    target: SOP-005
  - type: related-to
    target: SOP-007
  - type: related-to
    target: RISK-SEC-002
  - type: related-to
    target: RISK-SEC-003
  - type: related-to
    target: RISK-SEC-004
  - type: related-to
    target: ISSUE-184
  - type: related-to
    target: ISSUE-185
---

# SOUP Dependency List (Runtime)

## 1. Purpose

Identify major third-party software of unknown provenance (SOUP) used at runtime, with risk rationale and control approach suitable for this repository.

## 2. Scope

- Runtime dependencies used by production web app, backend services, and cloud functions.
- Development-only tooling (linters, test frameworks, formatters) excluded from this list.

## 3. SOUP Register

| Dependency                                              | Runtime Area            | Why Used                            | Primary Risk Rationale                                        | Key Controls                                                    |
| ------------------------------------------------------- | ----------------------- | ----------------------------------- | ------------------------------------------------------------- | --------------------------------------------------------------- |
| `firebase`                                              | Web                     | Client auth and API integration     | Authentication/session misuse if configured incorrectly       | Server-side auth verification, RBAC, no direct client writes    |
| `firebase-admin`                                        | Services/Functions/Data | Server access to Firebase resources | Privileged operations if misused                              | Least-privilege service config, audit logging, code review      |
| `firebase-functions`                                    | Services/Functions      | Cloud Functions runtime             | Public endpoint abuse and misconfiguration risk               | Endpoint validation, auth checks, webhook signing, CI tests     |
| `fastify`                                               | Services                | HTTP API framework                  | Input validation/auth bypass via route defects                | Zod validation, centralized auth middleware, integration tests  |
| `octokit`                                               | Services/Functions/Data | GitHub API integrations             | Token misuse, API behavior changes                            | Server-side token storage, limited scopes, monitoring           |
| `@google-cloud/tasks`                                   | Services/Functions      | Background processing orchestration | Task replay/duplication, orphan tasks                         | Idempotent workers, task status tracking, retry controls        |
| `@google-cloud/storage`                                 | Services/Functions      | Artifact and file storage           | Unauthorized object access or leakage                         | Access rules, signed access patterns, review of bucket policies |
| `zod`                                                   | Web/Services/Functions  | Schema validation for contracts     | Validation gaps if schemas drift                              | Shared contracts package, schema-first boundaries, CI checks    |
| `react` + `react-dom`                                   | Web                     | UI runtime                          | UI defects could hide critical compliance states              | E2E journeys for critical flows, release sign-off checks        |
| `react-router-dom`                                      | Web                     | Client routing                      | Route guard defects exposing unauthorized pages               | Auth-aware route guards, integration tests                      |
| `@tanstack/react-query`                                 | Web                     | Data fetching/cache                 | Stale or inconsistent compliance views                        | Explicit cache keys, invalidation strategy, polling for status  |
| `@mui/material`                                         | Web                     | Design system components            | Incorrect UI affordance for approval/compliance actions       | UX review and E2E checks on critical workflows                  |
| `pdf-lib`                                               | Services/Functions      | PDF generation/manipulation         | Corrupt/incorrect compliance report output                    | Output validation tests, deterministic generation checks        |
| `mermaid`                                               | Web/Functions           | Diagram rendering                   | Rendering/parsing failures, potential unsafe content handling | Controlled inputs, sanitize markdown sources                    |
| `gray-matter`                                           | Functions               | Frontmatter parsing                 | Parse failures can drop metadata/traceability                 | Graceful error handling, parser tests, sync status visibility   |
| `marked`                                                | Functions               | Markdown processing                 | Malformed markdown rendering or parser regressions            | Validation tests and controlled render pipeline                 |
| `@microsoft/microsoft-graph-client` + `@azure/identity` | Services/Functions/Data | Email/notification integrations     | Token handling and external API drift                         | Server-side credential management, failure monitoring           |

## 4. Review and Change Control

- Review this register at least quarterly and on every major dependency upgrade.
- For high-impact dependency upgrades, require:
  - linked GitHub issue,
  - PR evidence (tests + review),
  - release note entry if user-visible or compliance-relevant.
- If a dependency introduces a new major risk, create/update linked risk docs under `docs/risk/`.

## Revision History

| Version | Date       | Author          | Changes                       |
| ------- | ---------- | --------------- | ----------------------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial runtime SOUP register |
