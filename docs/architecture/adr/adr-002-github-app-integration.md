# ADR-002: GitHub App Integration

## Status

Accepted

## Date

2026-01-13

## Context

PactoSigna requires access to customer GitHub repositories to:

- Receive webhook events on push/PR
- Read markdown file content for display
- Parse frontmatter for metadata indexing

Three integration models were considered:

| Model                       | Description                                                                           |
| --------------------------- | ------------------------------------------------------------------------------------- |
| **GitHub App**              | Customer installs PactoSigna app on their GitHub org, grants access to specific repos |
| **OAuth + Manual Webhooks** | User authorizes via OAuth, manually configures webhook URLs per repo                  |
| **OAuth + API Webhooks**    | User authorizes via OAuth, we create webhooks using their token                       |

## Decision

**Use GitHub App integration model.**

### Implementation

1. Register PactoSigna as a GitHub App
2. Request permissions: `contents:read`, `metadata:read`
3. Subscribe to events: `push`, `pull_request`
4. User flow:
   - User clicks "Connect GitHub" in PactoSigna
   - Redirected to GitHub App installation flow
   - Selects organization and repositories to grant access
   - GitHub auto-configures webhooks
   - Redirected back to PactoSigna with installation ID

### GitHub App Configuration

```yaml
name: PactoSigna
description: AI-native QMS for medical device software
url: https://pactosigna.com

permissions:
  contents: read
  metadata: read
  pull_requests: read

events:
  - push
  - pull_request

webhook_url: https://api.pactosigna.com/webhooks/github
```

## Consequences

### Positive

- **Professional UX**: One-click installation, no manual webhook configuration
- **Reliable webhooks**: GitHub manages webhook delivery and retries
- **Granular permissions**: App only requests what it needs, scoped to selected repos
- **Installation tokens**: Short-lived tokens, more secure than long-lived OAuth tokens
- **Enterprise-ready**: GitHub Apps are the recommended integration method

### Negative

- **IT approval**: Enterprise customers may need IT to approve GitHub App installation
- **Setup complexity**: Requires registering and maintaining a GitHub App
- **Rate limits**: App installation tokens have rate limits (5000 req/hr typically sufficient)

### Security Considerations

- GitHub App private key must be securely stored (Cloud Secret Manager)
- Installation access tokens are short-lived and scoped
- No access to repos outside granted scope
