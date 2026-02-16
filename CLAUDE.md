# CLAUDE.md — PactoSigna.Docs

This is the Design History File (DHF) for PactoSigna. Every document here serves both regulatory compliance and day-to-day engineering. There is no distinction between "compliance docs" and "working docs" — they are the same thing.

## Document Format

All documents use YAML frontmatter:

```yaml
---
id: SRS-101
title: 'User Signup'
status: draft
links:
  - type: derives-from
    target: PRS-001
  - type: verified-by
    target: TC-001
---
```

### Required Fields
- `id` — unique document identifier matching the filename prefix
- `title` — human-readable title
- `status` — `draft`, `in_review`, `approved`, or `obsolete`

### Link Types
- `derives-from` — traces upstream (requirement → user need)
- `verified-by` — test coverage (requirement → test case)
- `implemented-by` — design implementing a requirement
- `mitigated-by` — risk mitigated by a control
- `related-to` — general relationship
- `summarizes` — aggregation document

## Folder Structure and ID Prefixes

Each folder has expected ID prefixes. Documents MUST use the correct prefix for their folder:

| Folder | Prefixes | Document Type |
|--------|----------|---------------|
| `docs/user-needs/` | `UN-` | User needs |
| `docs/product-requirements/` | `PRS-` | Product requirements |
| `docs/software-requirements/` | `SRS-` | Software requirements |
| `docs/architecture/` | `HLD-`, `SAD-` | Architecture |
| `docs/design/` | `SDD-`, `DDD-` | Detailed design |
| `docs/test/` | `TC-`, `TP-`, `PT-` | Test cases/plans |
| `docs/plans/` | `SMP-`, `CMP-`, `SDP-`, `RMP-`, `VVP-` | Lifecycle plans |
| `docs/risk/software/` | `RISK-SW-`, `HAZ-SW-` | Software risks |
| `docs/risk/security/` | `RISK-SEC-`, `HAZ-SEC-` | Security risks |
| `docs/risk/situations/` | `HS-` | Hazardous situations |
| `docs/risk/harms/` | `HARM-` | Harms |
| `docs/usability/use-specifications/` | `US-` | Use specifications |
| `docs/usability/task-analyses/` | `TA-` | Task analyses |
| `docs/usability/risks/` | `RISK-US-` | Usability risks |
| `docs/usability/evaluations/` | `UE-` | Usability evaluations |
| `docs/policies/` | `POL-` | Policies |
| `docs/sops/` | `SOP-` | Standard operating procedures |
| `docs/work-instructions/` | `WI-` | Work instructions |
| `docs/soup/` | `SOUP-` | SOUP dependency tracking |
| `docs/audits/` | `AUD-` | Audit reports |

## Creating New Documents

1. Determine the correct folder and prefix from the table above
2. Use the next available number: check existing files in the folder
3. Copy the template from `docs/templates/{PREFIX}-template.md`
4. Fill in frontmatter (`id`, `title`, `status`, `links`)
5. Write content following the template structure
6. Add traceability links to upstream and downstream documents

## Editing Workflow

1. Create a feature branch
2. Edit documents, ensuring frontmatter stays valid
3. Update cross-references if IDs or relationships change
4. Create a PR for human review

## Related Repository

The product source code lives in [PactoSigna.Experience.GitWeb](https://github.com/ords/PactoSigna.Experience.GitWeb). When implementing features, reference the relevant SRS and SDD documents from this repo.
