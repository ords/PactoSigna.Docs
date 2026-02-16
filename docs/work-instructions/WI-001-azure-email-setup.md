# Microsoft Graph API Email Setup

## Azure AD App Configuration

The email service uses Microsoft Graph API to send emails from `noreply@pactosigna.com`.

### Required API Permissions

In Azure Portal > App Registrations > Your App > API Permissions:

- **Microsoft Graph** > **Application permissions** > `Mail.Send`
- Click "Grant admin consent"

### App Credentials

You need:

- **Tenant ID** - Found in Azure Portal > Azure Active Directory > Overview
- **Client ID** - Found in App Registration > Overview
- **Client Secret** - Create in App Registration > Certificates & secrets

## Environment Variables

### Firebase Functions (Production)

```bash
firebase functions:secrets:set AZURE_TENANT_ID
firebase functions:secrets:set AZURE_CLIENT_ID
firebase functions:secrets:set AZURE_CLIENT_SECRET
```

Or using regular config:

```bash
firebase functions:config:set azure.tenant_id="your-tenant-id"
firebase functions:config:set azure.client_id="your-client-id"
firebase functions:config:set azure.client_secret="your-client-secret"
firebase functions:config:set graph.sender_email="noreply@pactosigna.com"
firebase functions:config:set app.url="https://qms.pactosigna.com"
```

### Local Development

Create `apps/functions/.env` (gitignored):

```
AZURE_TENANT_ID=your-tenant-id
AZURE_CLIENT_ID=your-client-id
AZURE_CLIENT_SECRET=your-client-secret
GRAPH_SENDER_EMAIL=noreply@pactosigna.com
APP_URL=http://localhost:5003
```

## Verification

Test email sending:

```typescript
import { sendInviteEmail } from './services/email.js';

await sendInviteEmail({
  to: 'your-email@example.com',
  inviterName: 'Test User',
  organizationName: 'Test Org',
  role: 'member',
  department: 'Engineering',
  inviteUrl: 'https://qms.pactosigna.com/invite/test-token',
});
```

## Troubleshooting

### "Insufficient privileges"

- Ensure `Mail.Send` application permission is granted
- Ensure admin consent was given

### "User not found"

- Verify `GRAPH_SENDER_EMAIL` is a valid mailbox in your tenant
- The user must exist (can be a shared mailbox)

### Token errors

- Verify tenant ID, client ID, and client secret are correct
- Check that the app registration is in the correct tenant
