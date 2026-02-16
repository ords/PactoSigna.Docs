# ADR-006: Metadata-Only Document Sync

## Status

Accepted

## Date

2026-01-13

## Context

When syncing documents from GitHub to Firestore, we need to decide what to store:

| Option           | Storage                       | Query Capability     | Content Freshness |
| ---------------- | ----------------------------- | -------------------- | ----------------- |
| Metadata only    | Frontmatter fields + path     | Limited              | N/A (no content)  |
| Full content     | Frontmatter + markdown body   | Full-text search     | May drift         |
| Metadata + links | Frontmatter + extracted links | Traceability queries | N/A (no content)  |

Key requirements:

1. **Traceability queries**: "Requirements without tests", "Trace chain for REQ-001"
2. **Document viewing**: Display markdown content with Mermaid diagrams
3. **Audit integrity**: Signed documents reference specific commit SHA
4. **Storage efficiency**: Minimize Firestore costs

## Decision

**Store metadata and links only. Fetch content from GitHub on demand.**

### What We Store (Firestore)

Documents are stored in a **nested collection under repositories** to enable efficient per-repository queries while supporting cross-repository queries via collection groups:

```typescript
// Documents nested under repositories
/organizations/{orgId}/repositories/{repoId}/documents/{docId}
{
  // Identity (denormalized for collection group queries)
  organizationId: string    // Required for collection group filtering
  repositoryId: string
  path: string              // "requirements/REQ-001.md"

  // Frontmatter metadata
  documentId: string        // "REQ-001" from frontmatter (user-facing ID)
  title: string
  docType: string           // "requirement", "architecture", "sop"
  status: string            // "draft", "approved"

  // Sync state
  currentCommitSha: string
  lastSyncedAt: Timestamp

  // Denormalized for display
  repositoryName: string
  repositoryType: 'qms' | 'device'
}

// Links stored at organization level
/organizations/{orgId}/links/{linkId}
{
  sourceDocumentId: string  // "REQ-001"
  targetDocumentId: string  // "TC-001"
  linkType: string          // "verified_by", "derives_from"
  createdAt: Timestamp

  // For reverse lookups
  sourceDocPath: string
  targetDocPath: string
}
```

**Important:** The Firestore document ID is a composite: `{orgId}_{repoId}_{documentId}`. The `documentId` field contains the user-facing ID from frontmatter (e.g., "REQ-001") used in URLs.

### Required Indexes

Collection group queries require explicit indexes. See `firebase/firestore.indexes.json`:

```json
// For querying documents across all repositories in an organization
{
  "collectionGroup": "documents",
  "queryScope": "COLLECTION_GROUP",
  "fields": [
    { "fieldPath": "organizationId", "order": "ASCENDING" },
    { "fieldPath": "lastSyncedAt", "order": "DESCENDING" }
  ]
}
```

### What We Fetch from GitHub (On Demand)

```typescript
// When viewing a document
const content = await github.repos.getContent({
  owner: org,
  repo: repoName,
  path: docPath,
  ref: commitSha, // Specific version or 'main' for latest
});

// When viewing a diff
const comparison = await github.repos.compareCommits({
  owner: org,
  repo: repoName,
  base: previousCommitSha,
  head: currentCommitSha,
});
```

### Sync Flow

```
GitHub Push Event (webhook)
         │
         ▼
Cloud Function: onGitHubPush
         │
         ├─► Fetch changed files from GitHub API
         │
         ├─► Parse frontmatter from markdown
         │
         ├─► Upsert document metadata to Firestore
         │
         ├─► Extract links from frontmatter
         │
         ├─► Update links collection (delete old, insert new)
         │
         └─► Create changelog entry (see ADR-009)
```

## Consequences

### Positive

- **Always fresh content**: No stale cache, GitHub is source of truth
- **Efficient storage**: Firestore stores ~500 bytes per doc, not full content
- **Fast traceability**: Links indexed for instant queries
- **Immutable references**: Commit SHA ensures exact version retrieval
- **Cost effective**: Firestore reads/writes are cheap, GitHub API is free for private repos

### Negative

- **GitHub dependency**: Document viewing requires GitHub API call
- **Latency**: ~100-200ms to fetch content from GitHub
- **Rate limits**: GitHub API has rate limits (5000/hr with App token)
- **No full-text search**: Can't search document body (only metadata)

### Mitigations

- **Caching**: Add CDN/Redis cache for frequently viewed documents
- **Prefetching**: Load linked documents in background
- **Rate limit monitoring**: Alert if approaching limits
- **Future**: Add Algolia/Typesense for full-text search if needed

### Traceability Query Examples

```typescript
// Query documents across all repositories in an organization (requires COLLECTION_GROUP index)
db.collectionGroup('documents')
  .where('organizationId', '==', orgId)
  .where('docType', '==', 'requirement')
  .orderBy('lastSyncedAt', 'desc')
  .get();

// Query documents within a specific repository (uses nested collection directly)
db.collection(`organizations/${orgId}/repositories/${repoId}/documents`)
  .where('docType', '==', 'requirement')
  .orderBy('lastSyncedAt', 'desc')
  .get();

// Find document by user-facing documentId (requires COLLECTION_GROUP index on documentId field)
db.collectionGroup('documents')
  .where('organizationId', '==', orgId)
  .where('documentId', '==', 'REQ-001')
  .limit(1)
  .get();

// Trace chain for document (using links at organization level)
async function getTraceChain(orgId: string, docId: string): Promise<TraceChain> {
  const upstream = await db
    .collection(`organizations/${orgId}/links`)
    .where('sourceDocumentId', '==', docId)
    .get();
  const downstream = await db
    .collection(`organizations/${orgId}/links`)
    .where('targetDocumentId', '==', docId)
    .get();
  // Recursively build chain...
}
```
