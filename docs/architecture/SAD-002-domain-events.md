# Domain Events

> ⚠️ **Design Specification**: Domain events are documented here as a design specification. They are not yet implemented as a runtime event system. Currently, side effects are handled directly in service methods and Cloud Functions.

Domain events represent significant state changes that may trigger side effects in other parts of the system.

## Event Naming Convention

- Past tense (something that happened)
- Format: `{AggregateType}{Action}`
- Examples: `OrganizationCreated`, `ReleasePublished`, `SignatureCollected`

---

## Identity & Access Events

### OrganizationCreated

Triggered when a new organization is created.

```typescript
interface OrganizationCreated {
  eventType: 'OrganizationCreated';
  timestamp: Date;
  payload: {
    organizationId: string;
    name: string;
    createdBy: string; // Firebase UID of creator
  };
}
```

**Side Effects:**

- Create default member for creator (admin role)
- Initialize audit log
- Send welcome email

### MemberInvited

Triggered when a user is invited to join an organization.

```typescript
interface MemberInvited {
  eventType: 'MemberInvited';
  timestamp: Date;
  payload: {
    inviteId: string;
    organizationId: string;
    email: string;
    role: 'admin' | 'member' | 'auditor';
    department: string;
    invitedBy: string;
  };
}
```

**Side Effects:**

- Send invite email with secure token
- Create audit log entry

### MemberJoined

Triggered when a user accepts an invitation.

```typescript
interface MemberJoined {
  eventType: 'MemberJoined';
  timestamp: Date;
  payload: {
    memberId: string;
    organizationId: string;
    userId: string;
    email: string;
    role: 'admin' | 'member' | 'auditor';
    department: string;
  };
}
```

**Side Effects:**

- Create audit log entry
- Send notification to admins

---

## Repository Events

### RepositoryConnected

Triggered when a GitHub repository is connected to an organization.

```typescript
interface RepositoryConnected {
  eventType: 'RepositoryConnected';
  timestamp: Date;
  payload: {
    repositoryId: string;
    organizationId: string;
    gitHubRepoFullName: string;
    ownerType: 'qms' | 'device';
    ownerId: string;
    connectedBy: string;
  };
}
```

**Side Effects:**

- Create/update QMS or Device entity
- Queue initial document sync
- Create audit log entry

### RepositorySynced

Triggered when a repository sync completes.

```typescript
interface RepositorySynced {
  eventType: 'RepositorySynced';
  timestamp: Date;
  payload: {
    repositoryId: string;
    organizationId: string;
    commitSha: string;
    documentsAdded: number;
    documentsUpdated: number;
    documentsDeleted: number;
  };
}
```

**Side Effects:**

- Update repository sync status
- Create audit log entry
- Notify relevant users (if configured)

---

## Document Events

### DocumentIndexed

Triggered when a document is indexed from GitHub.

```typescript
interface DocumentIndexed {
  eventType: 'DocumentIndexed';
  timestamp: Date;
  payload: {
    documentId: string;
    repositoryId: string;
    organizationId: string;
    path: string;
    title: string;
    docType: string;
    commitSha: string;
  };
}
```

**Side Effects:**

- Update traceability links
- Create changelog entry
- Create audit log entry

### DocumentUpdated

Triggered when an existing document is updated via sync.

```typescript
interface DocumentUpdated {
  eventType: 'DocumentUpdated';
  timestamp: Date;
  payload: {
    documentId: string;
    repositoryId: string;
    previousCommitSha: string;
    newCommitSha: string;
    changes: Array<{
      field: string;
      oldValue: unknown;
      newValue: unknown;
    }>;
  };
}
```

**Side Effects:**

- Append to changelog
- Update traceability if links changed
- Create audit log entry

---

## Release Events

### ReleaseCreated

Triggered when a new release is created.

```typescript
interface ReleaseCreated {
  eventType: 'ReleaseCreated';
  timestamp: Date;
  payload: {
    releaseId: string;
    organizationId: string;
    name: string;
    type: 'qms' | 'device';
    deviceId?: string;
    createdBy: string;
  };
}
```

**Side Effects:**

- Create audit log entry

### ReleaseSubmittedForReview

Triggered when a release is submitted for review.

```typescript
interface ReleaseSubmittedForReview {
  eventType: 'ReleaseSubmittedForReview';
  timestamp: Date;
  payload: {
    releaseId: string;
    organizationId: string;
    submittedBy: string;
    requiredDepartments: string[];
  };
}
```

**Side Effects:**

- Notify department reviewers
- Send review notifications to department members
- Create audit log entry

### SignatureCollected

Triggered when a signature is collected on a release.

```typescript
interface SignatureCollected {
  eventType: 'SignatureCollected';
  timestamp: Date;
  payload: {
    signatureId: string;
    releaseId: string;
    organizationId: string;
    signedBy: string;
    signatureType: 'author' | 'dept_reviewer' | 'final_approver';
    department: string;
    meaning: string;
  };
}
```

**Side Effects:**

- Check if all required signatures collected
- If complete, transition release to approved
- Create audit log entry

### ReleasePublished

Triggered when a release is published.

```typescript
interface ReleasePublished {
  eventType: 'ReleasePublished';
  timestamp: Date;
  payload: {
    releaseId: string;
    organizationId: string;
    publishedBy: string;
    documentCount: number;
    snapshotId: string;
  };
}
```

**Side Effects:**

- Capture traceability snapshot
- Update document effective versions
- Create audit log entry
- Send notification to all organization members
- Queue training tasks (future)

---

## Notification Events

### NotificationCreated

Triggered when a notification is persisted.

```typescript
interface NotificationCreated {
  eventType: 'NotificationCreated';
  timestamp: Date;
  payload: {
    notificationId: string;
    organizationId: string;
    recipientId: string;
    type: NotificationType;
    resourceType: string;
    resourceId: string;
  };
}
```

**Side Effects:**

- Send email to recipient if applicable

---

## Event Store (Future)

When implemented, events will be stored in a Firestore subcollection:

```
organizations/{orgId}/events/{eventId}
```

### Event Schema

```typescript
interface StoredEvent {
  id: string;
  eventType: string;
  aggregateType: string;
  aggregateId: string;
  timestamp: Timestamp;
  payload: Record<string, unknown>;
  metadata: {
    userId: string;
    correlationId: string;
    causationId?: string;
  };
}
```

### Querying Events

```typescript
// Get all events for an aggregate
const events = await db
  .collection('organizations')
  .doc(orgId)
  .collection('events')
  .where('aggregateId', '==', releaseId)
  .orderBy('timestamp', 'asc')
  .get();
```
