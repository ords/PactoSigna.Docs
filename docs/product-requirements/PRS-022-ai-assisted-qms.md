---
id: PRS-022
title: 'AI-Assisted Document Authoring & Analysis via MCP'
status: draft
links:
  - type: derives-from
    target: UN-010
---

# AI-Assisted Document Authoring & Analysis via MCP

## Requirement Statements

1. The system **SHALL** provide five cloud-hosted MCP servers: `qms-documents`, `qms-traceability`, `qms-risks`, `qms-training`, and `qms-releases`, each exposing domain-specific tools for AI consumption.

2. The system **SHALL** authenticate all MCP endpoint requests via per-organization API keys, ensuring that a key can only access its own organization's data.

3. The system **SHALL** allow organization administrators to create, rotate, and revoke MCP API keys from the application's Settings page.

4. The `qms-documents` MCP server **SHALL** expose tools for searching documents (by query, type, repo), retrieving individual documents, and listing documents by type.

5. The `qms-traceability` MCP server **SHALL** expose tools for finding traceability gaps, suggesting links (AI-powered), generating coverage matrices, and navigating full trace chains.

6. The `qms-risks` MCP server **SHALL** expose tools for querying risks by document, listing unmitigated risks, and generating risk matrices per device.

7. The `qms-training` MCP server **SHALL** expose tools for querying training status, listing overdue items, and generating completion reports.

8. The `qms-releases` MCP server **SHALL** expose tools for querying release history, current draft releases, and approval status.

## Rationale

PactoSigna's core differentiator is BYO AI via the Model Context Protocol. Rather than building a proprietary AI interface, exposing QMS data through MCP servers allows customers to use their preferred AI tools (Claude Desktop, GitHub Copilot, Cursor, Gemini) with their existing AI agreements. This eliminates vendor lock-in while enabling powerful AI-assisted workflows: engineers query traceability from their IDE, QA Managers analyze risk profiles conversationally, and product owners assess compliance status naturally. Per-organization API key authentication ensures data isolation between customers.

## Classification

| Field    | Value                         |
| -------- | ----------------------------- |
| Priority | Desirable                     |
| Category | Functional                    |
| Standard | N/A (differentiating feature) |

## Acceptance Criteria

1. Given a valid organization API key, when an MCP client connects to any of the five servers, then the connection succeeds and only that organization's data is accessible
2. Given an Admin on the Settings page, when they create a new API key, then the key is generated, displayed once, and usable immediately for MCP connections
3. Given an Admin, when they revoke an API key, then any MCP connection using that key is rejected on the next request
4. Given an MCP client calling `qms-documents.searchDocuments({query: "heart rate"})`, then documents matching "heart rate" in their title or content are returned with ID, title, type, and status
5. Given an MCP client calling `qms-traceability.findGaps({docType: "requirement"})`, then all Requirements without linked Tests are returned as a structured gap list
6. Given an MCP client calling `qms-risks.getUnmitigatedRisks({deviceId: "cardio-monitor"})`, then all open/unmitigated risks for the CardioMonitor device are returned
7. Given an MCP client calling `qms-training.getOverdueItems()`, then all training tasks past their due date are returned with member, document, due date, and days overdue
8. Given an invalid or revoked API key, when an MCP client attempts to connect, then the connection is rejected with a 401 Unauthorized response

## Traceability

| Direction | Link Type    | Target ID | Title                           |
| --------- | ------------ | --------- | ------------------------------- |
| Up        | derives-from | UN-010    | AI-Assisted QMS via MCP         |
| Related   | related-to   | PRS-008    | Document Linking & Traceability |
| Related   | related-to   | PRS-020    | Risk-Requirement Traceability   |
| Related   | related-to   | PRS-021    | Traceability Matrix Generation  |
| Down      | refined-by   | —         | _(To be linked by SWR-xxx)_     |
| Down      | verified-by  | —         | _(To be linked by TP-xxx)_      |

## Revision History

| Version | Date       | Author          | Changes       |
| ------- | ---------- | --------------- | ------------- |
| 0.1     | 2026-02-15 | Product Manager | Initial draft |
