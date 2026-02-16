---
id: UN-002
title: 'Repository Connection'
status: draft
links:
  - type: derives-from
    target: US-UP-002
  - type: derives-from
    target: epic-02-repository-connection
---

# Repository Connection

## Need Statement

> As a **Developer User**, I need **to connect my organization's GitHub repositories to PactoSigna and have document changes sync automatically** so that **QMS documents authored as markdown in Git are indexed without manual effort, keeping the QMS in sync with engineering workflows**.

## Context

PactoSigna's core differentiator is a developer-first approach where QMS documents live as markdown files in GitHub repositories. Engineers author and review documents via pull requests — their natural workflow — while PactoSigna indexes the metadata for regulatory activities. This need arises when an organization first onboards (connecting their QMS and device repositories) and whenever repositories are added, reconfigured, or disconnected.

The Developer User (Alex Park persona) expects the integration to "just work" — install a GitHub App, select repositories, classify them (QMS vs. device), and let automatic webhook-driven sync handle the rest. Manual re-sync should be available as a fallback. Each device repository must capture the device name and IEC 62304 safety class (A, B, or C) since Class C devices require additional document types (Software Detailed Design).

## Stakeholder

| Field   | Value                                                               |
| ------- | ------------------------------------------------------------------- |
| Persona | Developer User (Alex Park)                                          |
| Source  | Epic 02 — Repository Connection; Product Vision §GitHub Integration |

## Acceptance Criteria

1. An admin can install the PactoSigna GitHub App on their GitHub organization via the standard GitHub App installation flow
2. After installation, the admin can select which repositories to connect and classify each as QMS (one per org) or Device (multiple allowed)
3. Device repositories require a device name and safety class (A, B, or C)
4. Connected repositories trigger an initial sync that indexes all markdown documents
5. Subsequent pushes to connected repositories automatically sync changed files via GitHub webhooks
6. Webhook payloads are validated (signature verification) and processed idempotently
7. A sync status dashboard shows last sync time, document count, current status, and any errors per repository
8. Admins can trigger a manual re-sync at any time
9. Disconnecting a repository stops sync but preserves historical data
10. GitHub App uninstallation is handled gracefully with notifications to the admin

## Traceability

| Direction | Link Type      | Target ID                     | Title                            |
| --------- | -------------- | ----------------------------- | -------------------------------- |
| Up        | derives-from   | US-UP-002                     | Developer User Persona           |
| Down      | implemented-by | epic-02-repository-connection | Epic 2: Repository Connection    |
| Down      | implemented-by | STORY-201                     | Install GitHub App               |
| Down      | implemented-by | STORY-202                     | Select Repositories              |
| Down      | implemented-by | STORY-203                     | Manage Connected Repositories    |
| Down      | implemented-by | STORY-204                     | GitHub Webhook Handler           |
| Down      | implemented-by | STORY-205                     | Sync Status Dashboard            |
| Down      | implemented-by | STORY-206                     | Handle GitHub App Uninstallation |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
