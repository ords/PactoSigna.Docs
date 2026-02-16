---
id: UN-010
title: 'AI-Assisted QMS via MCP'
status: draft
links:
  - type: derives-from
    target: US-UP-001
  - type: derives-from
    target: US-UP-002
---

# AI-Assisted QMS via MCP

## Need Statement

> As a **QA Manager**, I need **to query my organization's QMS data using my preferred AI tools (Claude, GitHub Copilot, Cursor, Gemini) via cloud-hosted MCP servers** so that **I can quickly find traceability gaps, generate coverage reports, check training status, and analyze risk profiles without manually navigating the application**.

> As a **Developer User**, I need **to ask AI assistants questions about QMS relationships — such as "What requirements does HLD-002 implement?" or "Which requirements lack tests?" — using the tools already in my IDE** so that **compliance work integrates into my development workflow without context switching**.

## Context

PactoSigna's key differentiator is BYO AI via the Model Context Protocol (MCP). Rather than building a proprietary AI layer, PactoSigna exposes QMS data through cloud-hosted MCP servers that customers connect to their preferred AI tools. This eliminates vendor lock-in and leverages existing AI agreements.

Five MCP servers provide comprehensive access:

- **qms-documents**: Search, list, and read documents across all repositories
- **qms-traceability**: Query links, find gaps, generate coverage matrices, trace full chains
- **qms-risks**: Query risk assessments by type, linked items, and mitigation status
- **qms-training**: Training status, overdue items, completion reports
- **qms-releases**: Release history, current drafts, approval status

Each MCP server authenticates via per-organization API keys managed in the app settings. The tools enable natural language interactions: an engineer asks their AI "What requirements don't have tests?" and gets an actionable gap list; a QA Manager asks "Show the risk profile for the new monitoring feature" and gets a structured analysis; a product owner asks "What's the traceability status of UN-005?" and sees the full chain.

## Stakeholder

| Field   | Value                                                                            |
| ------- | -------------------------------------------------------------------------------- |
| Persona | QA Manager (Sarah Chen), Developer User (Alex Park), Product Owner               |
| Source  | Product Vision §AI Integration via MCP; Product Vision §Core Pillars (AI-native) |

## Acceptance Criteria

1. Five MCP servers are deployed and accessible: qms-documents, qms-traceability, qms-risks, qms-training, qms-releases
2. MCP endpoints authenticate via per-organization API keys
3. API keys can be created, rotated, and revoked from the app's Settings page
4. qms-documents server supports: searchDocuments, getDocument, listDocuments
5. qms-traceability server supports: findGaps, suggestLinks (AI-powered), getCoverageMatrix, getTraceabilityChain
6. qms-risks server supports: getRisksForDocument, getUnmitigatedRisks, getRiskMatrix
7. qms-training server supports querying training status, overdue items, and completion reports
8. qms-releases server supports querying release history, current drafts, and approval status
9. MCP servers are compatible with major AI tools (Claude Desktop, GitHub Copilot, Cursor, Gemini)
10. All MCP queries respect organization-level access controls — a key can only access its own organization's data

## Traceability

| Direction | Link Type    | Target ID | Title                      |
| --------- | ------------ | --------- | -------------------------- |
| Up        | derives-from | US-UP-001 | QA Manager Persona         |
| Up        | derives-from | US-UP-002 | Developer User Persona     |
| Related   | related-to   | UN-003    | Document Management & Sync |
| Related   | related-to   | UN-008    | Risk Management            |
| Related   | related-to   | UN-009    | Traceability Matrix        |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
