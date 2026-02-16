---
id: META-002
title: Document Registry
status: effective
links: []
---

# Document Registry

Master registry of all PactoSigna QMS documents. This is the single source of truth for document IDs, statuses, and locations.

**Last Updated**: 2026-02-15

---

## Architecture Documents

| ID       | Title                  | Status | Location                                               |
| -------- | ---------------------- | ------ | ------------------------------------------------------ |
| SAD-001  | Bounded Contexts       | Draft  | `docs/architecture/SAD-001-bounded-contexts.md`        |
| SAD-002  | Domain Events          | Draft  | `docs/architecture/SAD-002-domain-events.md`           |
| HLD-001 | System Context         | Draft  | `docs/architecture/HLD-001-system-context.md`         |
| HLD-002 | Deployment & Runtime   | Draft  | `docs/architecture/HLD-002-deployment-runtime.md`     |
| HLD-003 | Data Integration Flows | Draft  | `docs/architecture/HLD-003-data-integration-flows.md` |
| SAD-003 | Module Boundaries      | Draft  | `docs/architecture/SAD-003-module-boundaries.md`      |

## Architecture Decision Records

| ID      | Title                                            | Status    | Location                                                                  |
| ------- | ------------------------------------------------ | --------- | ------------------------------------------------------------------------- |
| adr-001 | Foundation Layer Priority                        | Effective | `docs/architecture/adr/adr-001-foundation-layer-priority.md`              |
| adr-002 | GitHub App Integration                           | Effective | `docs/architecture/adr/adr-002-github-app-integration.md`                 |
| adr-003 | Self-Service Multi-Tenancy                       | Effective | `docs/architecture/adr/adr-003-self-service-multi-tenancy.md`             |
| adr-004 | Email/Password Authentication                    | Effective | `docs/architecture/adr/adr-004-email-password-authentication.md`          |
| adr-005 | Department-Based Permissions                     | Effective | `docs/architecture/adr/adr-005-department-based-permissions.md`           |
| adr-006 | Metadata-Only Document Sync                      | Effective | `docs/architecture/adr/adr-006-metadata-only-document-sync.md`            |
| adr-007 | Hybrid Signature Flow                            | Effective | `docs/architecture/adr/adr-007-hybrid-signature-flow.md`                  |
| adr-008 | Backend-Only Firestore Writes                    | Effective | `docs/architecture/adr/adr-008-backend-only-firestore-writes.md`          |
| adr-009 | Document Changelog Subcollection                 | Effective | `docs/architecture/adr/adr-009-document-changelog-subcollection.md`       |
| adr-010 | MCP Deferred                                     | Effective | `docs/architecture/adr/adr-010-mcp-deferred.md`                           |
| adr-011 | REST API Migration                               | Effective | `docs/architecture/adr/adr-011-rest-api-migration.md`                     |
| adr-012 | Playwright BDD Testing                           | Effective | `docs/architecture/adr/adr-012-playwright-bdd-testing.md`                 |
| adr-013 | File Naming Conventions                          | Effective | `docs/architecture/adr/adr-013-file-naming-conventions.md`                |
| adr-014 | Shared Package Purpose                           | Effective | `docs/architecture/adr/adr-014-shared-package-purpose.md`                 |
| adr-015 | Issue-Driven AI Workflow                         | Effective | `docs/architecture/adr/adr-015-issue-driven-ai-workflow.md`               |
| adr-016 | External Service Abstraction                     | Effective | `docs/architecture/adr/adr-016-external-service-abstraction.md`           |
| adr-017 | Journey-Based E2E Testing                        | Effective | `docs/architecture/adr/adr-017-journey-based-e2e-testing.md`              |
| adr-018 | Server-Side Compliance Data                      | Effective | `docs/architecture/adr/adr-018-server-side-compliance-data.md`            |
| adr-019 | Entity Reference Validation                      | Effective | `docs/architecture/adr/adr-019-entity-reference-validation.md`            |
| adr-020 | ViewModel Hook Pattern                           | Effective | `docs/architecture/adr/adr-020-viewmodel-hook-pattern.md`                 |
| adr-021 | App Shell Loading Protocol                       | Effective | `docs/architecture/adr/adr-021-app-shell-loading-protocol.md`             |
| adr-022 | Docs Restructure — ISO 13485 & Agent Unification | Accepted  | `docs/architecture/adr/adr-022-docs-restructure-iso-agent-unification.md` |

## Design Documents

| ID      | Title                          | Status | Location                                              |
| ------- | ------------------------------ | ------ | ----------------------------------------------------- |
| DDD-001 | Aggregates & Entities          | Draft  | `docs/design/DDD-001-aggregates.md`                   |
| DDD-002 | Ubiquitous Language            | Draft  | `docs/design/DDD-002-ubiquitous-language.md`          |
| SDD-001  | Fastify Modules                | Draft  | `docs/design/SDD-001-fastify-modules.md`               |
| SDD-002  | Firestore Model & Repositories | Draft  | `docs/design/SDD-002-firestore-model-repositories.md`  |
| SDD-003  | Cloud Tasks Workers            | Draft  | `docs/design/SDD-003-cloud-tasks-workers.md`           |
| SDD-004  | Auth & Access Control          | Draft  | `docs/design/SDD-004-auth-and-access-control.md`       |
| SDD-005  | App Shell Provider Chain       | Draft  | `docs/design/SDD-005-app-shell-provider-chain.md`      |
| SDD-006  | ViewModel Hooks Data Fetching  | Draft  | `docs/design/SDD-006-viewmodel-hooks-data-fetching.md` |
| SDD-007  | Feature Module Structure       | Draft  | `docs/design/SDD-007-feature-module-structure.md`      |
| SDD-008  | Routing & Navigation Guards    | Draft  | `docs/design/SDD-008-routing-navigation-guards.md`     |

## Plans

| ID       | Title                         | Status | Location                                               |
| -------- | ----------------------------- | ------ | ------------------------------------------------------ |
| SMP-001 | Software Maintenance Plan     | Draft  | `docs/plans/SMP-001-software-maintenance-plan.md`     |
| CMP-001 | Configuration Management Plan | Draft  | `docs/plans/CMP-001-configuration-management-plan.md` |

## Standard Operating Procedures

| ID      | Title                                 | Status | Location                                                |
| ------- | ------------------------------------- | ------ | ------------------------------------------------------- |
| SOP-001 | Issue-Driven AI Development Workflow  | Draft  | `docs/sops/SOP-001-issue-driven-workflow.md`            |
| SOP-002 | Document Control                      | Draft  | `docs/sops/SOP-002-document-control.md`                 |
| SOP-003 | Design Control                        | Draft  | `docs/sops/SOP-003-design-control.md`                   |
| SOP-004 | Risk Management                       | Draft  | `docs/sops/SOP-004-risk-management.md`                  |
| SOP-005 | Software Development Lifecycle        | Draft  | `docs/sops/SOP-005-software-development-lifecycle.md`   |
| SOP-006 | Change Control                        | Draft  | `docs/sops/SOP-006-change-control.md`                   |
| SOP-007 | Verification & Validation             | Draft  | `docs/sops/SOP-007-verification-and-validation.md`      |
| SOP-008 | Training & Competency                 | Draft  | `docs/sops/SOP-008-training-and-competency.md`          |
| SOP-009 | Corrective & Preventive Action (CAPA) | Draft  | `docs/sops/SOP-009-corrective-and-preventive-action.md` |
| SOP-010 | Management Review                     | Draft  | `docs/sops/SOP-010-management-review.md`                |

## Work Instructions

| ID     | Title                                | Status | Location                                                              |
| ------ | ------------------------------------ | ------ | --------------------------------------------------------------------- |
| WI-001 | Microsoft Graph API Email Setup      | Draft  | `docs/work-instructions/WI-001-azure-email-setup.md`                  |
| WI-002 | Cloud Tasks Setup for Auditor Export | Draft  | `docs/work-instructions/WI-002-cloud-tasks-setup.md`                  |
| WI-003 | GitHub App Registration Guide        | Draft  | `docs/work-instructions/WI-003-github-app-setup.md`                   |
| WI-004 | GitHub App Installation Onboarding   | Draft  | `docs/work-instructions/WI-004-github-app-installation-onboarding.md` |
| WI-005 | Testing Best Practices               | Draft  | `docs/work-instructions/WI-005-test-best-practices.md`                |

## Quality Policies

| ID      | Title                       | Status | Location                                               |
| ------- | --------------------------- | ------ | ------------------------------------------------------ |
| POL-001 | Quality Policy              | Draft  | `docs/policies/POL-001-quality-policy.md`              |
| POL-002 | Quality Objectives          | Draft  | `docs/policies/POL-002-quality-objectives.md`          |
| POL-003 | Information Security Policy | Draft  | `docs/policies/POL-003-information-security-policy.md` |

## User Needs

| ID Range       | Title                                | Status | Location                    |
| -------------- | ------------------------------------ | ------ | --------------------------- |
| UN-001..UN-010 | User Needs baseline set (see README) | Draft  | `docs/user-needs/`          |
| UN-INDEX       | User Needs index                     | Draft  | `docs/user-needs/README.md` |

## Product Requirements

| ID Range       | Title                                          | Status | Location                              |
| -------------- | ---------------------------------------------- | ------ | ------------------------------------- |
| PRS-001..PRS-022 | Product Requirements baseline set (see README) | Draft  | `docs/product-requirements/`          |
| PR-INDEX       | Product Requirements index                     | Draft  | `docs/product-requirements/README.md` |

## Software Requirements

| ID Range         | Title                                                  | Status | Location                              |
| ---------------- | ------------------------------------------------------ | ------ | ------------------------------------- |
| SRS-101..SRS-711 | Software Requirements baseline set (1xx..7xx families) | Draft  | `docs/software-requirements/`         |
| SWR-INDEX        | Software Requirements index                            | Draft  | `docs/software-requirements/index.md` |

## Risk Documents

| ID            | Title                                                    | Status | Location                                                                  |
| ------------- | -------------------------------------------------------- | ------ | ------------------------------------------------------------------------- |
| RISK-MATRIX   | Risk Matrix Summary                                      | Draft  | `docs/risk/risk-matrix.md`                                                |
| RISK-SW-001   | Document Sync Data Loss                                  | Draft  | `docs/risk/software/RISK-SW-001-document-sync-data-loss.md`               |
| RISK-SW-002   | Electronic Signature Bypass                              | Draft  | `docs/risk/software/RISK-SW-002-signature-bypass.md`                      |
| RISK-SW-003   | Incorrect Audit Trail                                    | Draft  | `docs/risk/software/RISK-SW-003-incorrect-audit-trail.md`                 |
| RISK-SW-004   | Release with Unsigned Documents                          | Draft  | `docs/risk/software/RISK-SW-004-release-unsigned-documents.md`            |
| RISK-SW-005   | Training Assignment Failure                              | Draft  | `docs/risk/software/RISK-SW-005-training-assignment-failure.md`           |
| RISK-SW-006   | Incorrect Traceability                                   | Draft  | `docs/risk/software/RISK-SW-006-incorrect-traceability.md`                |
| RISK-SEC-001  | Penetration Testing Plan                                 | Draft  | `docs/risk/security/RISK-SEC-001-penetration-testing.md`                  |
| RISK-SEC-002  | Authentication Bypass                                    | Draft  | `docs/risk/security/RISK-SEC-002-authentication-bypass.md`                |
| RISK-SEC-003  | Webhook Tampering                                        | Draft  | `docs/risk/security/RISK-SEC-003-webhook-tampering.md`                    |
| RISK-SEC-004  | Token Exposure                                           | Draft  | `docs/risk/security/RISK-SEC-004-token-exposure.md`                       |
| RISK-SEC-005  | Privilege Escalation                                     | Draft  | `docs/risk/security/RISK-SEC-005-privilege-escalation.md`                 |
| HS-001  | Regulatory Submission with Incomplete Traceability       | Draft  | `docs/risk/situations/HS-001-incomplete-traceability-submission.md` |
| HS-002  | Audit with Tampered or Missing Audit Logs                | Draft  | `docs/risk/situations/HS-002-audit-missing-logs.md`                 |
| HS-003  | Released Software Without Required Training              | Draft  | `docs/risk/situations/HS-003-released-without-training.md`          |
| HARM-001 | Regulatory Non-Compliance                                | Draft  | `docs/risk/harms/HARM-001-regulatory-non-compliance.md`              |
| HARM-002 | Patient Safety Impact from Non-Compliant Device Software | Draft  | `docs/risk/harms/HARM-002-patient-safety-impact.md`                  |

## SOUP Documents

| ID         | Title                | Status | Location                                |
| ---------- | -------------------- | ------ | --------------------------------------- |
| SOUP-001   | SOUP Dependency List | Draft  | `docs/soup/SOUP-001-dependency-list.md` |
| SOUP-INDEX | SOUP Index           | Draft  | `docs/soup/README.md`                   |

## Test Documents

| ID       | Title                                                     | Status | Location                                                       |
| -------- | --------------------------------------------------------- | ------ | -------------------------------------------------------------- |
| TP-001   | E2E Testing Architecture                                  | Draft  | `docs/test/TP-001-e2e-test-plan.md`                            |
| TC-001   | Manual Test Run — 2026-02-11                              | Draft  | `docs/test/TC-001-manual-test-run-2026-02-11.md`               |
| TC-001 | Identity & Access — Authentication Backend Tests          | Draft  | `docs/test/TC-001-identity-authentication-backend.md`        |
| TC-002 | Identity & Access — Organization & Profile Frontend Tests | Draft  | `docs/test/TC-002-identity-organization-profile-frontend.md` |
| TC-003 | Identity & Access — Team Management & E2E Tests           | Draft  | `docs/test/TC-003-identity-team-management-e2e.md`           |
| TC-004 | Repository Connection — Backend & Webhook Tests           | Draft  | `docs/test/TC-004-repository-connection-backend.md`          |
| TC-005 | Repository Connection — Frontend & E2E Tests              | Draft  | `docs/test/TC-005-repository-connection-frontend-e2e.md`     |
| TC-006 | Document Management — Sync Engine & Parsing Tests         | Draft  | `docs/test/TC-006-document-sync-parsing.md`                  |
| TC-007 | Document Management — API & Service Tests                 | Draft  | `docs/test/TC-007-document-api-service.md`                   |
| TC-008 | Document Management — Frontend & E2E Tests                | Draft  | `docs/test/TC-008-document-frontend-e2e.md`                  |
| TC-009 | Audit Infrastructure — Backend Tests                      | Draft  | `docs/test/TC-009-audit-backend.md`                          |
| TC-010 | Audit Infrastructure — Frontend Tests                     | Draft  | `docs/test/TC-010-audit-frontend.md`                         |
| TC-011 | Release Management — Backend Tests                        | Draft  | `docs/test/TC-011-release-management-backend.md`             |
| TC-012 | Release Management — Frontend Tests                       | Draft  | `docs/test/TC-012-release-management-frontend.md`            |
| TC-013 | Release Management — E2E Tests                            | Draft  | `docs/test/TC-013-release-management-e2e.md`                 |
| TC-014 | Signature Collection — Backend Tests                      | Draft  | `docs/test/TC-014-signature-collection-backend.md`           |
| TC-015 | Signature Collection — Frontend Tests                     | Draft  | `docs/test/TC-015-signature-collection-frontend.md`          |
| TC-016 | Training Management — Backend & Worker Tests              | Draft  | `docs/test/TC-016-training-backend-workers.md`               |
| TC-017 | Training Management — Frontend Tests                      | Draft  | `docs/test/TC-017-training-frontend.md`                      |
| TC-018 | Training Management — E2E Tests                           | Draft  | `docs/test/TC-018-training-e2e.md`                           |
| TC-019 | Risk Management — Backend Tests                           | Draft  | `docs/test/TC-019-risk-management-backend.md`                |
| TC-020 | Risk Management — Frontend Tests                          | Draft  | `docs/test/TC-020-risk-management-frontend.md`               |
| TC-021 | Traceability & Notifications Verification                 | Draft  | `docs/test/TC-021-traceability-notifications.md`             |
| TC-022 | Cross-Cutting Infrastructure Tests                        | Draft  | `docs/test/TC-022-cross-cutting-infrastructure.md`           |
| TC-023 | PDF Export & Reporting Tests                              | Draft  | `docs/test/TC-023-pdf-export-reporting.md`                   |

## Task Analyses

| ID     | Title                                | Status | Location                                                           |
| ------ | ------------------------------------ | ------ | ------------------------------------------------------------------ |
| TA-001 | Document Management — User Journey   | Draft  | `docs/usability/task-analyses/TA-001-document-management.md`       |
| TA-002 | Release Management — User Journey    | Draft  | `docs/usability/task-analyses/TA-002-release-management.md`        |
| TA-003 | Risk Management — User Journey       | Draft  | `docs/usability/task-analyses/TA-003-risk-management.md`           |
| TA-004 | Organization Setup — User Journey    | Draft  | `docs/usability/task-analyses/TA-004-organization-setup.md`        |
| TA-005 | Audit & Compliance — User Journey    | Draft  | `docs/usability/task-analyses/TA-005-audit-compliance.md`          |
| TA-006 | External Signing — User Journeys     | Draft  | `docs/usability/task-analyses/TA-006-external-signing.md`          |
| TA-007 | Training Module — User Journey Maps  | Draft  | `docs/usability/task-analyses/TA-007-training-journeys.md`         |
| TA-008 | Training Module — Flow Specification | Draft  | `docs/usability/task-analyses/TA-008-training-interaction-spec.md` |

## Usability Risks

| ID     | Title                                  | Status | Location                                                                |
| ------ | -------------------------------------- | ------ | ----------------------------------------------------------------------- |
| RISK-US-001 | Complex Release Workflow               | Draft  | `docs/usability/risks/RISK-US-001-complex-release-workflow.md`               |
| RISK-US-002 | Training Task Confusion                | Draft  | `docs/usability/risks/RISK-US-002-training-task-confusion.md`                |
| RISK-US-003 | Document Sync Status Misinterpretation | Draft  | `docs/usability/risks/RISK-US-003-document-sync-status-misinterpretation.md` |
| RISK-US-004 | Risk Matrix Data Entry Errors          | Draft  | `docs/usability/risks/RISK-US-004-risk-matrix-data-entry-errors.md`          |
| RISK-US-005 | Signature Meaning Selection            | Draft  | `docs/usability/risks/RISK-US-005-signature-meaning-selection.md`            |
| RISK-US-006 | Audit Trail Navigation                 | Draft  | `docs/usability/risks/RISK-US-006-audit-trail-navigation.md`                 |

## Use Specifications

| ID        | Title                                                              | Status | Location                                                                         |
| --------- | ------------------------------------------------------------------ | ------ | -------------------------------------------------------------------------------- |
| US-DR-001 | UX Research: Auditor Access & External Signing                     | Draft  | `docs/usability/use-specifications/US-DR-001-external-signing-research.md`       |
| US-DR-002 | Redesign Proposal: Remove Auditor Role, Introduce Signing Requests | Draft  | `docs/usability/use-specifications/US-DR-002-external-signing-redesign.md`       |
| US-UN-001 | Training Module — Jobs-to-be-Done Analysis                         | Draft  | `docs/usability/use-specifications/US-UN-001-training-user-needs.md`             |
| US-UN-002 | External Signing — Jobs to Be Done                                 | Draft  | `docs/usability/use-specifications/US-UN-002-external-signing-user-needs.md`     |
| US-UP-001 | Persona: QA Manager                                                | Draft  | `docs/usability/use-specifications/US-UP-001-qa-manager.md`                      |
| US-UP-002 | Persona: Developer User                                            | Draft  | `docs/usability/use-specifications/US-UP-002-developer-user.md`                  |
| US-UP-003 | Persona: Regulatory Specialist                                     | Draft  | `docs/usability/use-specifications/US-UP-003-regulatory-specialist.md`           |
| US-UP-004 | Persona: Auditor                                                   | Draft  | `docs/usability/use-specifications/US-UP-004-auditor.md`                         |
| US-UV-001 | Document Management — Terminology                                  | Draft  | `docs/usability/use-specifications/US-UV-001-document-management-terminology.md` |
| US-UV-002 | Release Management — Terminology                                   | Draft  | `docs/usability/use-specifications/US-UV-002-release-management-terminology.md`  |
| US-UV-003 | Risk Management — Terminology                                      | Draft  | `docs/usability/use-specifications/US-UV-003-risk-management-terminology.md`     |
| US-UV-004 | Organization Setup — Terminology                                   | Draft  | `docs/usability/use-specifications/US-UV-004-organization-setup-terminology.md`  |
| US-UV-005 | Audit & Compliance — Terminology                                   | Draft  | `docs/usability/use-specifications/US-UV-005-audit-compliance-terminology.md`    |
| US-UV-006 | External Signing — Terminology                                     | Draft  | `docs/usability/use-specifications/US-UV-006-external-signing-terminology.md`    |

---

## Registry Statistics

| Category                | Count   | Status Breakdown         |
| ----------------------- | ------- | ------------------------ |
| Architecture (SAD/ARCH) | 6       | 6 Draft                  |
| ADRs                    | 22      | 21 Effective, 1 Accepted |
| Design (DDD/DD)         | 10      | 10 Draft                 |
| SOPs                    | 10      | 10 Draft                 |
| Work Instructions       | 5       | 5 Draft                  |
| Policies                | 3       | 3 Draft                  |
| User Needs              | 10      | 10 Draft                 |
| Product Requirements    | 22      | 22 Draft                 |
| Software Requirements   | 55      | 55 Draft                 |
| Risk Documents          | 17      | 17 Draft                 |
| Usability Risks         | 6       | 6 Draft                  |
| Test Documents          | 25      | 25 Draft                 |
| Task Analyses           | 8       | 8 Draft                  |
| Use Specifications      | 14      | 14 Draft                 |
| SOUP                    | 1       | 1 Draft                  |
| Plans                   | 2       | 2 Draft                  |
| **Total**               | **216** |                          |
