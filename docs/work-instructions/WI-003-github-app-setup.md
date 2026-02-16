# GitHub App Registration Guide

This guide walks you through registering the PactoSigna GitHub App for repository integration.

## Prerequisites

- GitHub organization admin access (or personal account)
- Firebase project deployed (for callback URLs)

## Step 1: Create the GitHub App

1. Go to **GitHub Settings** → **Developer settings** → **GitHub Apps** → **New GitHub App**
   - Or use this direct link: https://github.com/settings/apps/new

2. Fill in the basic information:
   - **GitHub App name**: `PactoSigna-<your-org-name>` (must be unique across GitHub)
   - **Homepage URL**: `https://pactosigna.web.app`
   - **Description**: "QMS document sync and compliance management"

## Step 2: Configure Callback URLs

Set the following URLs:

- **Callback URL**: `https://pactosigna.web.app/api/github/callback`
- **Setup URL** (optional): `https://pactosigna.web.app/repositories`
- **Webhook URL**: `https://pactosigna.web.app/api/github/webhook`

> **Note**: These URLs use Firebase Hosting rewrites (configured in `firebase.json`) to route requests to Cloud Functions, providing cleaner URLs under a single domain.

## Step 3: Configure Webhook

- **Webhook Active**: ✅ Enabled
- **Webhook Secret**: Generate a secure random string (save this for later)
  ```bash
  openssl rand -hex 32
  ```

## Step 4: Set Repository Permissions

Under **Repository permissions**, set:

| Permission        | Access Level | Reason                                |
| ----------------- | ------------ | ------------------------------------- |
| **Contents**      | Read-only    | Read markdown files for document sync |
| **Metadata**      | Read-only    | Access repo metadata                  |
| **Pull requests** | Read-only    | Track PR context for changes          |

## Step 5: Subscribe to Events

Under **Subscribe to events**, check:

- ✅ **Push** - Triggered when commits are pushed
- ✅ **Pull request** - Triggered when PRs are opened/merged
- ✅ **Installation** - Triggered when app is installed/uninstalled

## Step 6: Installation Settings

- **Where can this GitHub App be installed?**:
  - Select "Any account" for multi-tenant use
  - Or "Only on this account" for single organization

## Step 7: Create the App

Click **Create GitHub App** at the bottom of the page.

## Step 8: Generate Private Key

After creation:

1. Scroll down to **Private keys**
2. Click **Generate a private key**
3. Save the downloaded `.pem` file securely

## Step 9: Note Your App Credentials

Record these values from your App settings:

| Setting        | Value                                |
| -------------- | ------------------------------------ |
| App ID         | (shown at top of settings page)      |
| Client ID      | (shown in settings)                  |
| Private Key    | (contents of downloaded .pem file)   |
| Webhook Secret | (the secret you generated in Step 3) |

## Step 10: Store Secrets in Firebase

Store your secrets using Firebase Secret Manager:

```bash
# Set secrets
firebase functions:secrets:set GITHUB_APP_ID
# Enter your App ID when prompted

firebase functions:secrets:set GITHUB_APP_PRIVATE_KEY
# Paste the entire contents of your .pem file when prompted

firebase functions:secrets:set GITHUB_WEBHOOK_SECRET
# Enter your webhook secret when prompted
```

> **Important**: Secrets must also be declared in the Cloud Function's `secrets` array in `apps/services/src/index.ts` to be accessible at runtime. The GitHub secrets (`GITHUB_APP_ID`, `GITHUB_APP_PRIVATE_KEY`, `GITHUB_WEBHOOK_SECRET`) are already configured there.

## Step 11: Install the App

1. Go to your GitHub App's public page:
   - `https://github.com/apps/<your-app-name>`
2. Click **Install**
3. Select which repositories to grant access to
4. Click **Install**

## Verification

After installation, verify the setup:

1. Check Firebase Functions logs for the `installation` webhook event
2. In PactoSigna, navigate to Repositories and confirm the installation appears

## Troubleshooting

### Webhook returns 503 Service Unavailable

This means the webhook service is not properly configured. Check:

1. All three GitHub secrets are set: `GITHUB_APP_ID`, `GITHUB_APP_PRIVATE_KEY`, `GITHUB_WEBHOOK_SECRET`
2. The secrets are declared in the `secrets` array in `apps/services/src/index.ts`
3. The Cloud Function has been redeployed after adding secrets

### Webhook not receiving events

1. Check webhook delivery history in GitHub App settings
2. Verify the webhook URL is correct and accessible
3. Check Firebase Functions logs for errors

### Permission denied errors

1. Ensure the app is installed on the repository
2. Check that the correct permissions were granted
3. Reinstall the app if permissions were changed

### Private key issues

1. Make sure the entire .pem file contents were stored
2. Include the `-----BEGIN RSA PRIVATE KEY-----` and `-----END RSA PRIVATE KEY-----` lines
3. Check for trailing whitespace or newlines

## Security Best Practices

- ⚠️ Never commit private keys or secrets to version control
- ⚠️ Rotate secrets periodically
- ⚠️ Use the minimum required permissions
- ⚠️ Monitor webhook delivery for suspicious activity
