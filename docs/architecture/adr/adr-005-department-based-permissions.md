# ADR-005: Department-Based Permissions

## Status

Accepted

## Date

2026-01-13

## Context

PactoSigna needs a permission model that:

- Controls who can sign which document types
- Enforces departmental review requirements
- Supports configurable approval workflows
- Is simple enough for v1 but extensible

Options considered:

| Option           | Description                                                         | Complexity |
| ---------------- | ------------------------------------------------------------------- | ---------- |
| Simple roles     | Admin/Member/Auditor with fixed permissions                         | Low        |
| Department-based | Users belong to departments, doc types require department approvals | Medium     |
| Full RBAC        | Custom roles with granular per-resource permissions                 | High       |

The product vision specifies:

> Architecture documents → Architecture + Security departments
> Requirements documents → Engineering + Quality departments
> SOPs → Quality + affected department

This maps naturally to department-based permissions.

## Decision

**Implement department-based permissions with role modifiers.**

### Data Model

```typescript
// User membership
/organizations/{orgId}/members/{userId}
{
  email: string
  displayName: string
  department: string          // "Engineering", "Quality", "Regulatory", "Security"
  role: 'admin' | 'member' | 'auditor'
}

// Organization reviewer rules
/organizations/{orgId}/settings/reviewerRules
{
  rules: [
    {
      documentType: 'architecture',
      requiredDepartments: ['Architecture', 'Security'],
      finalApproverDepartment: 'Quality'
    },
    {
      documentType: 'requirements',
      requiredDepartments: ['Engineering', 'Quality'],
      finalApproverDepartment: 'Quality'
    },
    {
      documentType: 'sop',
      requiredDepartments: ['Quality'],
      finalApproverDepartment: 'Quality'
    }
  ]
}
```

### Permission Matrix

| Action                                 | Admin | Member | Auditor |
| -------------------------------------- | ----- | ------ | ------- |
| View published documents               | ✓     | ✓      | ✓       |
| View draft documents                   | ✓     | ✓      | ✗       |
| Create release                         | ✓     | ✗      | ✗       |
| Sign as reviewer (if in required dept) | ✓     | ✓      | ✗       |
| Sign as final approver                 | ✓     | ✗      | ✗       |
| Sign audit reports                     | ✓     | ✗      | ✓       |
| Manage org settings                    | ✓     | ✗      | ✗       |
| Invite users                           | ✓     | ✗      | ✗       |

### Signature Eligibility Logic

```typescript
function canSignAsReviewer(user: User, release: Release): boolean {
  const docTypes = release.changes.map(c => c.documentType);
  const requiredDepts = getRequiredDepartments(docTypes);
  return requiredDepts.includes(user.department);
}

function canSignAsFinalApprover(user: User, release: Release): boolean {
  return user.role === 'admin' && user.department === getOrgSettings().finalApproverDepartment;
}
```

## Consequences

### Positive

- **Matches QMS reality**: Regulated companies organize by department
- **Configurable**: Orgs define their own reviewer rules
- **Simple mental model**: Users understand "my department reviews X"
- **Audit-friendly**: Clear trail of who was eligible to sign
- **Extensible**: Can add more departments without code changes

### Negative

- **Single department**: Users can only belong to one department (may need multi-department in future)
- **No per-document overrides**: Can't say "this specific doc needs CEO approval"
- **Department naming**: Must standardize or allow custom department names

### Future Extensions

- Multi-department membership
- Per-release ad-hoc reviewer assignment
- Delegation (user A can sign on behalf of user B)

## Status Update (2026-02-05)

The organization-level reviewer rules described above were never wired into the
release or signature workflow. Instead:

- **Release creation** accepts `requiredDepartments` directly from the user
- **Document reviewers/approvers** are defined in markdown frontmatter
- The Settings UI "Reviewer Rules" tab was removed as dead functionality

If organization-level reviewer rule enforcement is needed in the future, it should
be implemented as validation in `ReleasesService.createRelease()` that cross-references
org settings when determining required signers.
