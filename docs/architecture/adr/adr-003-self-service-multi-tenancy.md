# ADR-003: Self-Service Multi-Tenancy

## Status

Accepted

## Date

2026-01-13

## Context

PactoSigna needs to support multiple customer organizations, each with isolated data. We need to decide how organizations are created and how users join them.

Three models were considered:

| Model                   | Description                                                   |
| ----------------------- | ------------------------------------------------------------- |
| **Self-service signup** | User signs up → Creates org → Connects GitHub → Invites team  |
| **Admin-provisioned**   | PactoSigna admin creates org → Sends invite link → Users join |
| **GitHub Org mapping**  | GitHub OAuth login → Auto-create org matching GitHub org      |

## Decision

**Use self-service signup model.**

### User Onboarding Flow

```
1. User visits pactosigna.com
         │
         ▼
2. Clicks "Sign Up" → Creates account (email/password)
         │
         ▼
3. "Create Organization" wizard
   - Organization name
   - Industry (medical devices)
   - Regulatory frameworks (ISO 13485, IEC 62304)
         │
         ▼
4. "Connect GitHub" → GitHub App installation flow
         │
         ▼
5. "Invite Team" → Send email invites with org join link
         │
         ▼
6. Dashboard with connected repos
```

### Firestore Structure

```
/organizations/{orgId}
  name: string
  createdBy: string (userId)
  createdAt: Timestamp
  settings: {
    frameworks: string[]
    reviewerRules: {...}
  }
  gitHubAppInstallationId: number

/organizations/{orgId}/members/{userId}
  email: string
  displayName: string
  department: string
  role: 'admin' | 'member' | 'auditor'
  invitedBy: string
  joinedAt: Timestamp
```

### Invite Flow

1. Admin generates invite link (contains orgId + invite token)
2. New user clicks link → Signs up or logs in
3. Backend validates invite token → Adds user to organization
4. Invite token invalidated (single-use or time-limited)

## Consequences

### Positive

- **Low friction**: Users can start immediately without contacting sales
- **Scalable**: No manual provisioning required
- **Standard SaaS model**: Users understand this pattern
- **Supports trials**: Easy to offer free tier or trial period

### Negative

- **Org sprawl risk**: Same company might create multiple orgs accidentally
- **No enterprise controls**: IT can't pre-provision orgs (addressed with SSO later)
- **Invite management**: Need to build invite system

### Mitigations

- Org name uniqueness check (suggest existing if similar name)
- Future: Domain-based org discovery for enterprise SSO
