# PactoSigna Design Document

## Product Overview & Core Value

**PactoSigna** is an AI-native Quality Management System designed for medical device software companies (ISO 13485 / IEC 62304 compliant).

### The Problem

QARA teams manage QMS in isolation using Word documents and SharePoint, disconnected from engineering workflows. This creates:

- Stale documentation that doesn't reflect reality
- Painful traceability efforts before audits
- Engineers who see QMS as bureaucratic overhead
- No intelligent assistance for any role

### The Solution

Bring QMS into developer workflows (Git, markdown, PRs) while providing a dedicated application for regulatory activities (signatures, training, releases, audit support).

### Core Pillars

1. **Developer-first** - Documents as markdown in GitHub, managed via PRs
2. **Compliance automation** - Part 11 signatures, traceability matrices, audit trails
3. **Team collaboration** - Engineers and QARA in the same workflow
4. **AI-native** - MCP servers expose QMS data to customer's AI tools (Claude, Copilot, Gemini)

### Users

- **Software Engineers** - Create/update docs via PRs, query QMS via AI
- **QARA** - Manage releases, approvals, training, run compliance reports
- **Product Owners** - Understand risk/complexity, generate feature ideas via AI
- **External Auditors** - Sign audit reports, view released documentation

### Key Differentiator

BYO AI via MCP - customers connect their preferred AI tools to query and analyze their QMS. No vendor lock-in, uses their existing AI agreements.

### Documentation Format

- All documents as markdown files in GitHub
- Architecture diagrams as Mermaid (rendered in-app)
- Version-controllable, diff-friendly, PR-reviewable

---

## Document Model & Traceability

### Document Types by Repository

**QMS Repository:**

| Type             | Purpose                              | Training Required                              |
| ---------------- | ------------------------------------ | ---------------------------------------------- |
| SOP              | Standard Operating Procedures        | Yes (quiz first time, configurable on updates) |
| Work Instruction | Detailed steps (links to parent SOP) | Yes                                            |
| Policy           | High-level organizational policies   | Yes                                            |

**Device Repository (one per device):**

| Type                     | Purpose                                  | Class C Only |
| ------------------------ | ---------------------------------------- | ------------ |
| User Needs               | What users need to achieve               | No           |
| Requirements             | What the system must do                  | No           |
| Architecture             | System structure, components, interfaces | No           |
| Software Detailed Design | Implementation details                   | Yes          |
| Tests                    | Verification evidence                    | No           |

### Risk Types & Linking

| Risk Type      | Analysis Method                             | Links To                |
| -------------- | ------------------------------------------- | ----------------------- |
| Usability Risk | IEC 62366 (use errors, harm)                | User Needs              |
| Software Risk  | FMEA (severity, probability, detectability) | Requirements            |
| Security Risk  | STRIDE                                      | Architecture/Components |

### Traceability Model

```
User Needs → Requirements → Architecture → Detailed Design → Tests
     ↓              ↓              ↓
Usability Risk  Software Risk  Security Risk
```

Each link is stored in Firestore with metadata (link type, rationale, created by, timestamp).

---

## Repository Structure & Markdown Format

### QMS Repository Structure

```
/sops/
  SOP-001-document-control.md
  SOP-002-design-control.md
/work-instructions/
  WI-001-pr-review-process.md
/policies/
  POL-001-quality-policy.md
```

### Device Repository Structure

```
/user-needs/
  UN-001-monitor-vitals.md
/requirements/
  REQ-001-heart-rate-display.md
  REQ-002-alarm-threshold.md
/architecture/
  HLD-001-system-overview.md
  HLD-002-data-pipeline.md
  /diagrams/
    system-context.mermaid
/detailed-design/          # Class C only
  SDD-001-signal-processing.md
/tests/
  TC-001-heart-rate-accuracy.md
/risks/
  /usability/
    RISK-US-001-alarm-fatigue.md
  /software/
    SR-001-incorrect-reading.md
  /security/
    SEC-001-data-tampering.md
```

### Markdown Document Format

Frontmatter + content:

```markdown
---
id: REQ-001
title: Heart Rate Display
status: draft
links:
  - type: derives-from
    target: UN-001
  - type: verified-by
    target: TC-001
---

## Description

The system shall display heart rate in BPM...

## Acceptance Criteria

- Display updates within 2 seconds
- Range: 30-250 BPM
```

Frontmatter provides machine-readable metadata. Links in frontmatter are synced to Firestore for fast querying.

---

## GitHub Integration & Two-Stage Review

### Source of Truth

- **GitHub**: Document content (markdown files)
- **Firestore**: Metadata, signatures, training records, traceability index, audit logs

### Sync Mechanism

GitHub webhooks notify your app on push/PR events. App parses markdown frontmatter and updates Firestore index.

### Two-Stage Review Process

**Stage 1: Technical Review (GitHub)**

```
Engineer creates branch
    ↓
Opens PR with changes
    ↓
Peers review content/accuracy
    ↓
PR approved & merged to main
```

**Stage 2: Formal Approval (PactoSigna App)**

```
Release Manager creates "Quality Controlled Change Release"
    ↓
Selects merged changes to include (from one or more repos)
    ↓
System identifies required departmental reviewers (based on document types)
    ↓
Reviewers receive notifications, sign with Part 11 compliant signatures
    ↓
Final Approver signs
    ↓
Release published → becomes auditable baseline
```

### Branching Policy

Configurable per organization. Recommended default: require PR for all changes. Organizations can allow direct commits if they choose.

### Conflict Handling

If frontmatter links conflict with Firestore (manual edit vs app edit), app flags for resolution. GitHub content always wins for document body.

---

## Signatures & Part 11 Compliance

### 21 CFR Part 11 Requirements

| Requirement                | Implementation                                               |
| -------------------------- | ------------------------------------------------------------ |
| Unique user identification | Firebase Auth (email + password or SSO)                      |
| Tamper-evident audit trail | Firestore audit log (immutable, timestamped)                 |
| Signature meaning          | Role captured: Author, Reviewer, Approver, Trainee, Auditor  |
| Time-stamped               | Server-side timestamp on all signatures                      |
| Signature binding          | Signature tied to specific document version (Git commit SHA) |

### Signature Types

| Type     | Who                | When                                            |
| -------- | ------------------ | ----------------------------------------------- |
| Author   | Engineer/QARA      | Document created/updated in PR                  |
| Reviewer | Department members | Formal approval stage (per document type rules) |
| Approver | QA Manager/RA      | Final release sign-off                          |
| Trainee  | Staff              | Training completion                             |
| Auditor  | External party     | Audit report sign-off (PDF)                     |

### Reviewer Assignment Rules

Configured per organization:

```
Architecture documents → Architecture + Security departments
Requirements documents → Engineering + Quality departments
SOPs → Quality + affected department
```

System enforces all required departments have signed before release can be approved.

### Signature Record

Stored in Firestore:

```typescript
{
  userId: string;
  documentId: string;
  commitSha: string;
  signatureType: 'author' | 'reviewer' | 'approver' | 'trainee' | 'auditor';
  meaning: string; // "I have reviewed and approve this document"
  timestamp: Timestamp;
  department: string;
}
```

---

## Training Management

### Training Triggers

| Scenario             | Training Type      | Triggered By                            |
| -------------------- | ------------------ | --------------------------------------- |
| First time on SOP/WI | Quiz               | User assigned to document or role       |
| Major update         | Quiz               | Release manager marks update as "major" |
| Minor update         | Read & Acknowledge | Release manager marks update as "minor" |

### Training Workflow

```
Release published with SOP/WI changes
    ↓
System identifies affected staff (by role/department)
    ↓
Training tasks created (quiz or acknowledge based on update type)
    ↓
Staff receives notification
    ↓
Staff reads document in app
    ↓
Staff completes quiz OR clicks "I have read and understood"
    ↓
Part 11 signature captured (Trainee type)
    ↓
Training record stored
```

### Quiz Management

- Quizzes stored as markdown in the repo alongside the SOP/WI
- Questions in frontmatter or separate file (e.g., `SOP-001-quiz.md`)
- Configurable passing score per organization
- Failed quiz → can retry after re-reading

### Training Record

Firestore:

```typescript
{
  userId: string
  documentId: string
  documentVersion: string  // commit SHA
  trainingType: 'quiz' | 'acknowledge'
  quizScore?: number
  completedAt: Timestamp
  signature: SignatureRecord
}
```

### Training Dashboard

- Overdue training alerts
- Completion reports by department
- Audit-ready training matrix export

---

## Release Management

### Quality Controlled Change Release

A release is a formal bundle of document changes that go through approval together. Organizations can release QMS documents, device documents, or both in a single release.

### Release Types

| Type             | Scope                                    | Example                |
| ---------------- | ---------------------------------------- | ---------------------- |
| QMS Release      | SOPs, WIs, Policies from QMS repo        | "QMS-2024-Q1"          |
| Device Release   | All document types from one device repo  | "CardioMonitor-v2.1.0" |
| Combined Release | Changes across QMS + one or more devices | "Annual-Review-2024"   |

### Release Lifecycle

```
Draft → In Review → Approved → Published
```

| State     | Description                                      |
| --------- | ------------------------------------------------ |
| Draft     | Release manager selecting changes, adding notes  |
| In Review | Departmental reviewers signing                   |
| Approved  | All signatures collected, awaiting final publish |
| Published | Immutable baseline, visible to auditors          |

### Release Record

Firestore:

```typescript
{
  id: string
  name: string
  type: 'qms' | 'device' | 'combined'
  deviceId?: string
  status: 'draft' | 'in_review' | 'approved' | 'published'
  changes: [{
    repoId: string
    documentId: string
    commitSha: string
    changeType: 'added' | 'modified' | 'removed'
  }]
  signatures: SignatureRecord[]
  publishedAt?: Timestamp
  publishedBy?: string
}
```

### Audit View

Auditors see published releases as immutable snapshots with full signature history.

---

## AI Integration via MCP

### Model Context Protocol (MCP) Servers

Cloud-hosted MCP endpoints. Customers connect their preferred AI tools (Claude Desktop, GitHub Copilot, Cursor, etc.) to query their QMS.

### MCP Servers

| Server             | Capabilities                                         |
| ------------------ | ---------------------------------------------------- |
| `qms-documents`    | Read, search, list documents across all repos        |
| `qms-traceability` | Query links, find gaps, generate coverage matrices   |
| `qms-risks`        | Query risk assessments by type, linked items, status |
| `qms-training`     | Training status, overdue items, completion reports   |
| `qms-releases`     | Release history, current drafts, approval status     |

### Example MCP Tools Exposed

```typescript
// qms-documents
searchDocuments(query: string, docType?: string, repo?: string)
getDocument(documentId: string)
listDocuments(docType: string, repo?: string)

// qms-traceability
findGaps(docType: string)  // "Requirements without tests"
suggestLinks(documentId: string)  // AI-powered suggestions
getCoverageMatrix(fromType: string, toType: string)
getTraceabilityChain(documentId: string)  // Full upstream/downstream

// qms-risks
getRisksForDocument(documentId: string)
getUnmitigatedRisks(riskType?: string)
getRiskMatrix(deviceId: string)
```

### Authentication

MCP endpoints authenticated via API key per organization. Keys managed in app settings.

### Use Cases by Role

- **Engineer**: "What requirements does HLD-002 implement?"
- **QARA**: "Show me all requirements without linked tests"
- **PO**: "What's the risk profile for the new monitoring feature?"

---

## Technical Architecture

### Monorepo Structure

```
/apps
  /web                 # React + TypeScript frontend
  /functions           # Firebase Cloud Functions
/packages
  /shared              # Shared types, utilities
  /mcp-server          # MCP server implementation
/firebase
  firestore.rules
  storage.rules
```

### Frontend (React + TypeScript)

- Vite for build tooling
- React Router for navigation
- Device switcher in header (QMS or specific device context)
- Mermaid.js for diagram rendering
- Markdown preview with frontmatter parsing

### Firebase Services

| Service         | Purpose                                              |
| --------------- | ---------------------------------------------------- |
| Auth            | User authentication (email/password, SSO)            |
| Firestore       | Metadata, signatures, training, releases, audit logs |
| Cloud Functions | GitHub webhooks, MCP endpoints, notifications        |
| Storage         | PDF exports, audit report uploads                    |
| Hosting         | Frontend deployment                                  |

### Key Cloud Functions

| Function                 | Trigger   | Purpose                                  |
| ------------------------ | --------- | ---------------------------------------- |
| `onGitHubPush`           | Webhook   | Sync document changes to Firestore index |
| `onGitHubPR`             | Webhook   | Track PR status, notify reviewers        |
| `mcpHandler`             | HTTP      | MCP protocol endpoint                    |
| `onReleasePublish`       | Firestore | Trigger training tasks, notifications    |
| `generateCoverageReport` | HTTP      | Build traceability matrix                |

### External Integrations

- GitHub API (repo access, PR management)
- GitHub OAuth (connect user's repos)

---

## Data Model Summary

### Firestore Collections

```
/organizations/{orgId}
  name, settings, reviewerRules, gitHubOrgId

/organizations/{orgId}/users/{userId}
  email, name, department, roles

/organizations/{orgId}/repos/{repoId}
  type: 'qms' | 'device'
  deviceName?: string
  gitHubRepo: string
  safetyClass?: 'A' | 'B' | 'C'

/organizations/{orgId}/documents/{docId}
  repoId, docType, path, title, status
  currentCommitSha, lastSyncedAt

/organizations/{orgId}/links/{linkId}
  sourceDocId, targetDocId, linkType
  createdBy, createdAt, rationale

/organizations/{orgId}/releases/{releaseId}
  name, type, status, changes[], signatures[]
  publishedAt, publishedBy

/organizations/{orgId}/signatures/{sigId}
  userId, documentId, releaseId, commitSha
  signatureType, meaning, timestamp, department

/organizations/{orgId}/training/{trainingId}
  userId, documentId, documentVersion
  trainingType, quizScore, completedAt, signatureId

/organizations/{orgId}/auditLog/{logId}
  action, userId, resourceType, resourceId
  timestamp, details
```

### Indexes for Common Queries

- Documents by type and repo
- Links by source or target document
- Training by user, by document, overdue
- Signatures by release, by user

---

## User Interface Overview

### Navigation

```
┌─────────────────────────────────────────────────────┐
│ PactoSigna    [Device Switcher ▼]    [User Menu]   │
├─────────────────────────────────────────────────────┤
│ Documents | Traceability | Risks | Releases |      │
│ Training | Settings                                 │
└─────────────────────────────────────────────────────┘
```

### Device Switcher Options

- QMS (SOPs, WIs, Policies)
- CardioMonitor (device)
- InsulinPump (device)
- etc.

### Key Screens

| Screen          | Purpose                                                               |
| --------------- | --------------------------------------------------------------------- |
| Documents       | Browse/search documents, view markdown with rendered Mermaid diagrams |
| Document Detail | View content, frontmatter, linked items, signature history            |
| Traceability    | Interactive matrix view, gap detection, coverage reports              |
| Risks           | Risk registers by type, linked documents, mitigation status           |
| Releases        | Create/manage releases, track approval progress                       |
| Release Detail  | View changes, collect signatures, publish                             |
| Training        | My training tasks, completion history                                 |
| Training Admin  | Assign training, view org-wide status, overdue alerts                 |
| Settings        | Org config, repo connections, reviewer rules, API keys                |

### Document Viewer Features

- Markdown rendering with syntax highlighting
- Mermaid diagram rendering inline
- Frontmatter displayed as metadata panel
- Linked documents as clickable chips
- Version history (Git commits)
- Signature status indicator

---

## Summary

### Core Capabilities

- Documents as markdown in GitHub (QMS repo + device repos)
- Two-stage review: GitHub PRs for technical, app for formal Part 11 approval
- Quality Controlled Change Releases (QMS, device, or combined)
- Three risk types linked to appropriate document types
- Training management with quiz/acknowledge based on change significance
- Cloud-hosted MCP servers for BYO AI integration
- Mermaid diagram support for architecture visualization

### Tech Stack

- React + TypeScript + Vite (frontend)
- Firebase: Auth, Firestore, Cloud Functions, Storage, Hosting
- GitHub API + OAuth
- MCP protocol for AI integration

### Key Decisions

- GitHub as source of truth for content
- One repo per device, one QMS repo per org
- Device switcher UI (focused context)
- Release-based formal approvals
- Configurable reviewer rules per document type
- BYO AI via MCP (no vendor lock-in)
