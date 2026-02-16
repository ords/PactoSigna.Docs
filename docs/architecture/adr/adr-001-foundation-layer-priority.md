# ADR-001: Foundation Layer Priority

## Status

Accepted

## Date

2026-01-13

## Context

PactoSigna has multiple capabilities described in the product vision:

- GitHub sync and document viewing
- Traceability matrix and gap detection
- Release management with Part 11 signatures
- Training management with quizzes
- AI integration via MCP servers
- Auditor access and reporting

We need to determine the build order to deliver value incrementally while respecting technical dependencies.

### Dependency Analysis

```
GitHub Sync + Document Index
         │
         ▼
    Document Viewer
         │
    ┌────┴────┐
    ▼         ▼
Traceability  Release Management
              │
         ┌────┴────┐
         ▼         ▼
     Training   Auditor View
```

## Decision

**Build the foundation layer first:**

1. **GitHub OAuth + Repo Connection** - Users can connect their GitHub organization
2. **GitHub App Installation** - Auto-configure webhooks for selected repositories
3. **Webhook Receiver** - Cloud Function to receive push events
4. **Markdown Parser** - Extract frontmatter metadata and links
5. **Firestore Indexer** - Store document metadata for querying
6. **Document List/Detail Viewer** - Browse and read documents in the app

This foundation is required before any other features can function meaningfully.

### Deferred Capabilities (in rough priority order)

1. Traceability queries and gap detection
2. Release management and signatures
3. Training management
4. Auditor access
5. MCP integration (lowest priority)

## Consequences

### Positive

- Clear build order with no blocked dependencies
- Early value delivery: users can view their QMS documents in-app
- Foundation enables rapid iteration on higher-level features
- Validates GitHub App integration early (critical path)

### Negative

- Users cannot perform formal approvals until Release Management is built
- No compliance value until signatures are implemented
- Must clearly communicate MVP scope to early customers

### Risks

- If GitHub App approval is slow in enterprise orgs, could delay adoption
- Mitigation: Document IT approval requirements early in sales process
