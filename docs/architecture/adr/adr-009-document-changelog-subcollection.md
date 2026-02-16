# ADR-009: Document Changelog Subcollection

## Status

Accepted

## Date

2026-01-13

## Context

Auditors and compliance officers need to answer:

1. "Show me the history of REQ-001"
2. "What changed in Release 2.0?"
3. "Who changed this document and when?"
4. "What was the document at version 1.0?"

We need to store document change history while avoiding content duplication (Git stores content).

Options considered:

| Option                 | Structure                                | Query Pattern         |
| ---------------------- | ---------------------------------------- | --------------------- |
| Document subcollection | `/documents/{docId}/changelog/{entryId}` | History with document |
| Release-centric        | Changes on release record                | Query by release      |
| Separate collection    | `/changelog/{entryId}` with indexes      | Flexible queries      |

## Decision

**Store changelog as subcollection per document. Content diffs come from Git.**

### Data Model

```typescript
// Changelog entry per document
/organizations/{orgId}/documents/{docId}/changelog/{entryId}
{
  // Git reference
  commitSha: string              // Git commit that introduced change
  previousCommitSha: string      // For diff generation (null if first version)

  // Change metadata
  changeType: 'added' | 'modified' | 'removed'
  changedBy: string              // userId (matched by commit author email)
  changedByEmail: string         // Git commit author email
  changedAt: Timestamp           // Commit timestamp

  // PR context (if available)
  pullRequestNumber?: number
  pullRequestTitle?: string

  // Release context (populated when release published)
  releaseId?: string             // null until included in release
  releaseName?: string           // Denormalized for display
  includedInReleaseAt?: Timestamp

  // Frontmatter changes (for quick display without fetching Git)
  metadataChanges?: {
    field: string
    oldValue: any
    newValue: any
  }[]

  // Change summary (auto-generated or from PR description)
  changeSummary?: string
}
```

### Sync Flow

```
PR Merged to Main
       │
       ▼
GitHub Webhook → Cloud Function: onGitHubPush
       │
       ├─► Get list of changed files from push payload
       │
       ├─► For each changed markdown file:
       │   │
       │   ├─► Parse frontmatter (current version)
       │   │
       │   ├─► Upsert document metadata
       │   │
       │   └─► Create changelog entry:
       │       {
       │         commitSha: push.after,
       │         previousCommitSha: push.before,
       │         changeType: 'modified',
       │         changedBy: matchUserByEmail(commit.author.email),
       │         changedAt: commit.timestamp,
       │         releaseId: null  // Not yet released
       │       }
       │
       └─► Update links collection


Release Published
       │
       ▼
Cloud Function: onReleasePublish
       │
       └─► For each document in release:
           │
           └─► Update changelog entries where releaseId is null
               AND commitSha is in release.changes[].commitSha:
               {
                 releaseId: release.id,
                 releaseName: release.name,
                 includedInReleaseAt: serverTimestamp()
               }
```

### Query Patterns

```typescript
// 1. Document history (most recent first)
db.collection(`organizations/${orgId}/documents/${docId}/changelog`)
  .orderBy('changedAt', 'desc')
  .limit(50);

// 2. Changes in a release (requires collection group index)
db.collectionGroup('changelog').where('releaseId', '==', releaseId).orderBy('changedAt', 'desc');

// 3. Unreleased changes
db.collectionGroup('changelog').where('releaseId', '==', null).orderBy('changedAt', 'desc');

// 4. Changes by user
db.collectionGroup('changelog').where('changedBy', '==', userId).orderBy('changedAt', 'desc');
```

### Content Diff Retrieval

```typescript
// When user clicks "View diff" in UI
async function getContentDiff(
  orgId: string,
  docId: string,
  changelogEntry: ChangelogEntry
): Promise<Diff> {
  const doc = await db.doc(`organizations/${orgId}/documents/${docId}`).get();
  const { repoName, path } = doc.data();

  // Use GitHub Compare API
  const comparison = await github.repos.compareCommits({
    owner: orgGitHubOwner,
    repo: repoName,
    base: changelogEntry.previousCommitSha,
    head: changelogEntry.commitSha,
  });

  // Find the specific file in the comparison
  const fileDiff = comparison.data.files.find(f => f.filename === path);

  return {
    patch: fileDiff.patch,
    additions: fileDiff.additions,
    deletions: fileDiff.deletions,
  };
}
```

## Consequences

### Positive

- **Document-centric queries**: Easy to get full history of any document
- **No content duplication**: Git stores content, we store metadata
- **Immutable commit references**: Can always reconstruct exact state
- **Release association**: Know which release included which changes
- **Efficient storage**: ~200 bytes per changelog entry

### Negative

- **Collection group queries**: Need indexes for cross-document queries
- **User matching**: Must match Git email to PactoSigna user (may fail)
- **Two-step diff**: Must call GitHub API to see actual content changes

### Firestore Indexes Required

```javascript
// firestore.indexes.json
{
  "indexes": [
    {
      "collectionGroup": "changelog",
      "queryScope": "COLLECTION_GROUP",
      "fields": [
        { "fieldPath": "releaseId", "order": "ASCENDING" },
        { "fieldPath": "changedAt", "order": "DESCENDING" }
      ]
    },
    {
      "collectionGroup": "changelog",
      "queryScope": "COLLECTION_GROUP",
      "fields": [
        { "fieldPath": "changedBy", "order": "ASCENDING" },
        { "fieldPath": "changedAt", "order": "DESCENDING" }
      ]
    }
  ]
}
```

### UI Display

```
┌─────────────────────────────────────────────────────────────┐
│ REQ-001: Heart Rate Display                                 │
├─────────────────────────────────────────────────────────────┤
│ History                                                     │
│                                                             │
│ ● v2.1.0 (2026-01-10) - Jane Smith                         │
│   └─ Updated acceptance criteria [View diff]                │
│                                                             │
│ ● v2.0.0 (2025-11-15) - John Doe                           │
│   └─ Added BPM range requirement [View diff]               │
│                                                             │
│ ● v1.0.0 (2025-08-01) - Jane Smith                         │
│   └─ Initial version                                        │
└─────────────────────────────────────────────────────────────┘
```
