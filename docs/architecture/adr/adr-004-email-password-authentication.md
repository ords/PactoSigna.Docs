# ADR-004: Email/Password Authentication with SSO Path

## Status

Accepted

## Date

2026-01-13

## Context

PactoSigna requires user authentication that satisfies:

- 21 CFR Part 11: "Unique user identification"
- Accessibility for all user types (Engineers, QARA, Auditors)
- Enterprise compatibility

User types and their GitHub relationship:

| User Type | Has GitHub Account? | Primary Activity                  |
| --------- | ------------------- | --------------------------------- |
| Engineer  | Yes                 | View docs, query via AI           |
| QARA      | Maybe               | Manage releases, sign documents   |
| Auditor   | Unlikely            | View releases, sign audit reports |
| Executive | Unlikely            | Approve releases                  |

Options considered:

| Option                       | Pros                            | Cons                               |
| ---------------------------- | ------------------------------- | ---------------------------------- |
| Email/Password only          | Universal, simple               | No SSO, password fatigue           |
| GitHub OAuth only            | Single identity                 | Excludes non-GitHub users          |
| GitHub OAuth + Email         | Flexible                        | Identity reconciliation complexity |
| Email/Password + SSO (later) | Universal now, enterprise later | Two-phase implementation           |

## Decision

**Start with Email/Password authentication via Firebase Auth, architect for SSO addition.**

### Implementation

1. **Firebase Auth with Email/Password provider**
   - Email verification required
   - Password strength requirements enforced
   - Password reset via email

2. **User record structure**

   ```typescript
   // Firebase Auth manages: uid, email, password hash, email verified

   // Firestore user profile
   /organizations/{orgId}/members/{firebaseUid}
   {
     email: string
     displayName: string
     department: string
     role: 'admin' | 'member' | 'auditor'
     // Future: ssoProvider, ssoSubjectId
   }
   ```

3. **GitHub identity is separate**
   - GitHub App installation is org-level, not user-level
   - User doesn't need GitHub account to use PactoSigna
   - If user has GitHub, we can link commits to their email (author attribution)

### SSO Preparation

Architect for future SAML/OIDC support:

- User identity is Firebase UID, not email
- Email is a property, not the primary key
- Organization settings will include SSO configuration
- Firebase Auth supports custom OIDC providers

## Consequences

### Positive

- **Universal access**: All user types can authenticate
- **Part 11 compliant**: Unique identification via Firebase UID
- **Auditor-friendly**: External auditors sign documents without GitHub
- **Simple MVP**: No OAuth complexity for initial release
- **SSO-ready**: Clean path to enterprise SSO

### Negative

- **Password management**: Users have another password to manage
- **No GitHub identity linking**: Can't auto-associate GitHub commits to PactoSigna users (must match by email)
- **Enterprise expectation**: Some customers may expect SSO from day one

### Part 11 Compliance Notes

| Requirement           | Implementation                        |
| --------------------- | ------------------------------------- |
| Unique identification | Firebase UID (immutable)              |
| Password complexity   | Firebase Auth enforced + custom rules |
| Account lockout       | Firebase Auth built-in                |
| Session management    | Firebase Auth tokens with expiration  |
