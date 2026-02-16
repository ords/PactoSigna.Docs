# ADR-010: MCP Integration Deferred

## Status

Accepted

## Date

2026-01-13

## Context

The product vision includes AI integration via Model Context Protocol (MCP) servers:

- Cloud-hosted MCP endpoints
- Customers connect their preferred AI tools (Claude Desktop, Copilot, Cursor)
- Query QMS data via natural language

This is a key differentiator ("BYO AI via MCP - no vendor lock-in") but requires:

- Stable data model and API
- MCP server implementation
- Authentication via API keys
- Rate limiting and usage tracking
- Documentation for customer integration

## Decision

**Defer MCP implementation to post-MVP. Focus on core QMS functionality first.**

### Priority Order

1. ✅ **Foundation Layer** (MVP)
   - GitHub App integration
   - Document sync and viewing
   - User/org management

2. **Traceability** (Post-MVP Phase 1)
   - Link indexing
   - Gap detection
   - Coverage matrices

3. **Release Management** (Post-MVP Phase 2)
   - Release creation
   - Part 11 signatures
   - Publishing workflow

4. **Training Management** (Post-MVP Phase 3)
   - Quiz/acknowledge flows
   - Training records
   - Compliance reports

5. **MCP Integration** (Post-MVP Phase 4)
   - MCP server implementation
   - API key management
   - Usage analytics

### Rationale

1. **Dependency**: MCP queries against QMS data. Data must exist and be stable first.
2. **Value delivery**: Core QMS features deliver compliance value without AI.
3. **Market validation**: Validate core product before building differentiator.
4. **Technical stability**: Data model may evolve. MCP API should be stable.
5. **Resource focus**: Limited bandwidth, prioritize must-haves over nice-to-haves.

### Architecture Preparation

Even though deferred, we'll design for MCP-readiness:

```typescript
// All data access via service layer (not direct Firestore)
// This service layer becomes the MCP server's backend

interface DocumentService {
  searchDocuments(query: string, filters?: DocFilters): Promise<Document[]>;
  getDocument(docId: string): Promise<Document>;
  getDocumentContent(docId: string, commitSha?: string): Promise<string>;
}

interface TraceabilityService {
  findGaps(docType: string): Promise<Gap[]>;
  getCoverageMatrix(fromType: string, toType: string): Promise<Matrix>;
  getTraceChain(docId: string): Promise<TraceChain>;
}

interface RiskService {
  getRisksForDocument(docId: string): Promise<Risk[]>;
  getUnmitigatedRisks(riskType?: string): Promise<Risk[]>;
}

// Future: MCP server wraps these services
// mcpServer.tool('search_documents', DocumentService.searchDocuments)
// mcpServer.tool('find_gaps', TraceabilityService.findGaps)
```

## Consequences

### Positive

- **Focus**: Team focuses on core compliance value
- **Stability**: MCP API will be stable when launched
- **Quality**: No premature optimization for AI queries
- **Validation**: Market feedback on core before investing in AI

### Negative

- **Differentiation delayed**: Key differentiator not available at launch
- **Marketing constraint**: Can't lead with "AI-native" messaging initially
- **Technical debt risk**: May need to refactor for MCP compatibility

### Success Criteria for MCP Phase

Before starting MCP implementation:

- [ ] Document sync stable and performant
- [ ] Traceability queries optimized
- [ ] At least 3 customers actively using core features
- [ ] Data model frozen (no breaking changes)
- [ ] Service layer abstraction complete

### Future MCP Architecture (Reference)

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│ Claude Desktop  │     │ GitHub Copilot  │     │ Cursor          │
└────────┬────────┘     └────────┬────────┘     └────────┬────────┘
         │                       │                       │
         └───────────────────────┼───────────────────────┘
                                 │
                                 ▼
                    ┌─────────────────────────┐
                    │ PactoSigna MCP Server   │
                    │ (Cloud Functions)       │
                    │                         │
                    │ - API Key Auth          │
                    │ - Rate Limiting         │
                    │ - Usage Tracking        │
                    └────────────┬────────────┘
                                 │
                                 ▼
                    ┌─────────────────────────┐
                    │ Service Layer           │
                    │                         │
                    │ - DocumentService       │
                    │ - TraceabilityService   │
                    │ - RiskService           │
                    │ - TrainingService       │
                    └────────────┬────────────┘
                                 │
                                 ▼
                    ┌─────────────────────────┐
                    │ Firestore + GitHub API  │
                    └─────────────────────────┘
```
