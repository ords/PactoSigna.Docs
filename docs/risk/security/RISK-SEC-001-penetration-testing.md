# Penetration Testing Plan

## Document Information

| Field        | Value      |
| ------------ | ---------- |
| Version      | 1.0        |
| Created      | 2026-01-15 |
| Last Updated | 2026-01-15 |
| Status       | Draft      |

---

## 1. Executive Summary

This document outlines the penetration testing plan for PactoSigna QMS, focusing on identified attack surfaces, test scenarios, and remediation priorities. The primary areas of concern are publicly accessible Cloud Functions that handle GitHub integration.

---

## 2. Scope

### 2.1 In-Scope Assets

| Asset              | Type                    | URL/Endpoint               |
| ------------------ | ----------------------- | -------------------------- |
| Firebase Hosting   | Web Application         | https://qms.pactosigna.com |
| onGitHubWebhook    | Cloud Function (Public) | /api/github/webhook        |
| onGitHubCallback   | Cloud Function (Public) | /api/github/callback       |
| Firestore Database | Backend                 | N/A (no direct access)     |
| Firebase Auth      | Authentication          | N/A                        |

### 2.2 Out-of-Scope

- GitHub's infrastructure
- Firebase/GCP infrastructure vulnerabilities
- Physical security
- Social engineering

---

## 3. Threat Model

### 3.1 Public Cloud Functions

The following functions are configured with `invoker: 'public'` to allow unauthenticated access:

```
┌─────────────────────────────────────────────────────────────────┐
│                        INTERNET                                  │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│              Firebase Hosting (qms.pactosigna.com)              │
│                                                                  │
│  /api/github/webhook  ──────►  onGitHubWebhook (Cloud Run)      │
│  /api/github/callback ──────►  onGitHubCallback (Cloud Run)     │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### 3.2 Attack Surface Analysis

| Function         | Authentication        | Authorization | Data Sensitivity       |
| ---------------- | --------------------- | ------------- | ---------------------- |
| onGitHubWebhook  | HMAC-SHA256 Signature | None          | Medium (repo metadata) |
| onGitHubCallback | None                  | None          | Low (installation IDs) |

---

## 4. Test Scenarios

### 4.1 onGitHubWebhook - Webhook Receiver

#### TEST-WH-001: Signature Bypass Attempts

| ID                     | TEST-WH-001                                            |
| ---------------------- | ------------------------------------------------------ |
| **Objective**          | Verify webhook signature validation cannot be bypassed |
| **Priority**           | Critical                                               |
| **Current Protection** | HMAC-SHA256 with `GITHUB_WEBHOOK_SECRET`               |

**Test Cases:**

1. **Missing signature header**
   - Send request without `X-Hub-Signature-256`
   - Expected: 401/403 rejection

2. **Invalid signature**
   - Send request with malformed signature
   - Expected: 401/403 rejection

3. **Wrong secret signature**
   - Sign payload with different secret
   - Expected: 401/403 rejection

4. **Empty payload with valid-looking signature**
   - Test edge cases in signature verification
   - Expected: Rejection or safe handling

5. **Timing attack on signature comparison**
   - Measure response times for different signatures
   - Expected: Constant-time comparison (already using `timingSafeEqual`)

---

#### TEST-WH-002: Replay Attacks

| ID                     | TEST-WH-002                                   |
| ---------------------- | --------------------------------------------- |
| **Objective**          | Determine if webhook payloads can be replayed |
| **Priority**           | Medium                                        |
| **Current Protection** | None                                          |
| **Status**             | ⚠️ VULNERABLE                                 |

**Test Cases:**

1. **Capture and replay**
   - Intercept valid webhook, replay same payload
   - Expected: Should be rejected (currently not)

2. **Delayed replay**
   - Replay webhook after significant delay
   - Expected: Should be rejected based on timestamp

**Recommended Remediation:**

```typescript
// Store X-GitHub-Delivery in Firestore with TTL
const deliveryId = req.headers['x-github-delivery'];
const seen = await db.collection('webhook_deliveries').doc(deliveryId).get();
if (seen.exists) {
  res.status(409).send('Duplicate delivery');
  return;
}
await db.collection('webhook_deliveries').doc(deliveryId).set(
  {
    receivedAt: FieldValue.serverTimestamp(),
  },
  { merge: true }
);
// TTL via Firestore TTL policy (e.g., 24 hours)
```

---

#### TEST-WH-003: DDoS / Cost Exhaustion

| ID                     | TEST-WH-003                                |
| ---------------------- | ------------------------------------------ |
| **Objective**          | Assess vulnerability to volumetric attacks |
| **Priority**           | Medium                                     |
| **Current Protection** | None                                       |
| **Status**             | ⚠️ AT RISK                                 |

**Test Cases:**

1. **High volume invalid requests**
   - Send 10,000 requests/minute with invalid signatures
   - Expected: Measure cost impact, response degradation

2. **Slow loris style**
   - Hold connections open with slow data
   - Expected: Timeout handling

**Recommended Remediation:**

- Configure Cloud Armor WAF rules
- Set `maxInstances` limit on function
- Implement IP allowlisting for GitHub webhook IPs:
  ```
  https://api.github.com/meta → hooks array
  ```

---

#### TEST-WH-004: IP Source Validation

| ID                     | TEST-WH-004                               |
| ---------------------- | ----------------------------------------- |
| **Objective**          | Verify requests can only come from GitHub |
| **Priority**           | Low                                       |
| **Current Protection** | None (relies on signature)                |

**Test Cases:**

1. **Request from non-GitHub IP**
   - With valid signature (if secret leaked)
   - Expected: Should still be accepted (signature is primary control)

**Recommended Enhancement (Defense in Depth):**

```typescript
// Fetch GitHub IPs periodically and cache
const GITHUB_WEBHOOK_IPS = await fetch('https://api.github.com/meta')
  .then(r => r.json())
  .then(d => d.hooks);

// Check client IP against allowlist
const clientIp = req.headers['x-forwarded-for']?.split(',')[0];
if (!GITHUB_WEBHOOK_IPS.some(cidr => isInCidr(clientIp, cidr))) {
  logger.warn('Request from non-GitHub IP', { clientIp });
  // Log but don't reject - signature is authoritative
}
```

---

### 4.2 onGitHubCallback - OAuth Redirect Handler

#### TEST-CB-001: Open Redirect

| ID                     | TEST-CB-001                           |
| ---------------------- | ------------------------------------- |
| **Objective**          | Verify no open redirect vulnerability |
| **Priority**           | High                                  |
| **Current Protection** | Hardcoded `appUrl`                    |
| **Status**             | ✅ NOT VULNERABLE                     |

**Test Cases:**

1. **Inject redirect parameter**
   - `/api/github/callback?redirect=https://evil.com`
   - Expected: Ignored, redirects to hardcoded domain

2. **Path traversal in installation_id**
   - `/api/github/callback?installation_id=../../../etc/passwd`
   - Expected: Safe handling (used only in query string)

---

#### TEST-CB-002: CSRF on OAuth Flow

| ID                     | TEST-CB-002                                |
| ---------------------- | ------------------------------------------ |
| **Objective**          | Check for CSRF in OAuth state              |
| **Priority**           | Medium                                     |
| **Current Protection** | None                                       |
| **Status**             | ⚠️ POTENTIAL RISK (if code exchange added) |

**Analysis:**

Currently, the callback only handles redirects after installation, not OAuth code exchange. The `code` parameter is not used.

**If code exchange is added later:**

```typescript
// Generate state before redirect to GitHub
const state = crypto.randomBytes(32).toString('hex');
await db
  .collection('oauth_states')
  .doc(state)
  .set({
    userId: currentUser.uid,
    expiresAt: Date.now() + 600000, // 10 minutes
  });

// In callback, verify state
const storedState = await db.collection('oauth_states').doc(req.query.state).get();
if (!storedState.exists || storedState.data().expiresAt < Date.now()) {
  res.status(403).send('Invalid state');
  return;
}
```

---

#### TEST-CB-003: Unused Secrets Exposure

| ID                     | TEST-CB-003                          |
| ---------------------- | ------------------------------------ |
| **Objective**          | Identify unnecessary secret exposure |
| **Priority**           | Low                                  |
| **Current Protection** | N/A                                  |
| **Status**             | ⚠️ UNNECESSARY EXPOSURE              |

**Finding:**

The `onGitHubCallback` function declares these secrets but never uses them:

- `GITHUB_CLIENT_ID`
- `GITHUB_CLIENT_SECRET`

**Remediation:**

Remove unused secrets from function configuration:

```typescript
export const onGitHubCallback = onRequest(
  {
    // Remove: secrets: [githubClientId, githubClientSecret],
    cors: false,
    invoker: 'public',
  }
  // ...
);
```

---

### 4.3 Firestore Security Rules

#### TEST-FS-001: Direct Database Access

| ID            | TEST-FS-001                                  |
| ------------- | -------------------------------------------- |
| **Objective** | Verify Firestore rules enforce proper access |
| **Priority**  | Critical                                     |

**Test Cases:**

1. **Unauthenticated read**
   - Attempt to read any collection without auth
   - Expected: Denied

2. **Cross-organization access**
   - Authenticated user tries to read other org's data
   - Expected: Denied

3. **Role escalation**
   - Member tries to modify their own role
   - Expected: Denied

---

## 5. Remediation Priority Matrix

| Priority | ID          | Issue                           | Effort | Status                        |
| -------- | ----------- | ------------------------------- | ------ | ----------------------------- |
| High     | TEST-WH-002 | Replay attack protection        | Medium | 🔴 TODO                       |
| High     | TEST-CB-003 | Remove unused secrets           | Low    | 🔴 TODO                       |
| Medium   | TEST-WH-003 | Rate limiting / DDoS protection | Medium | 🔴 TODO                       |
| Medium   | TEST-CB-002 | OAuth state validation          | Low    | 🟡 N/A (no code exchange yet) |
| Low      | TEST-WH-004 | IP allowlisting                 | Medium | 🔴 TODO                       |

---

## 6. Monitoring & Alerting

### 6.1 Recommended Alerts

| Alert               | Trigger               | Action                         |
| ------------------- | --------------------- | ------------------------------ |
| High webhook volume | > 100 requests/minute | Investigate, consider blocking |
| Signature failures  | > 10 failures/hour    | Potential attack, review IPs   |
| 5xx error spike     | > 5% error rate       | Investigate function health    |
| Cost anomaly        | > 200% daily average  | Review usage, check for abuse  |

### 6.2 Log Queries

```sql
-- Failed signature attempts
resource.type="cloud_run_revision"
resource.labels.service_name="ongithubwebhook"
textPayload:"signature verification failed"

-- All webhook deliveries
resource.type="cloud_run_revision"
resource.labels.service_name="ongithubwebhook"
jsonPayload.message="GitHub webhook received"
```

---

## 7. Testing Schedule

| Phase              | Target Date | Scope                          |
| ------------------ | ----------- | ------------------------------ |
| Initial Assessment | 2026-01-20  | Automated scans, manual review |
| Remediation        | 2026-01-27  | Fix high/medium priority items |
| Verification       | 2026-02-03  | Re-test fixed items            |
| Ongoing            | Monthly     | Automated vulnerability scans  |

---

## 8. Appendix

### A. GitHub Webhook IP Ranges

Fetch current ranges from: `https://api.github.com/meta`

```json
{
  "hooks": [
    "192.30.252.0/22",
    "185.199.108.0/22",
    "140.82.112.0/20",
    "143.55.64.0/20",
    ...
  ]
}
```

### B. Tools

- **OWASP ZAP** - Web application scanner
- **Burp Suite** - HTTP interception and manipulation
- **nuclei** - Vulnerability scanner
- **Firebase Local Emulator** - Local testing

### C. References

- [Firebase Security Checklist](https://firebase.google.com/support/guides/security-checklist)
- [Cloud Run Security Best Practices](https://cloud.google.com/run/docs/securing/service-identity)
- [GitHub Webhooks Security](https://docs.github.com/en/webhooks/using-webhooks/best-practices-for-using-webhooks)
