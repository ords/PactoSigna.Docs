# Design: ISO 13485 Design Document Alignment Enforcement

**Date**: 2026-02-09
**Status**: Draft
**Problem**: No agent, skill, or CI gate enforces that ISO 13485 design documents (HLD, SDD, domain docs) stay current when features ship. The Signing Requests bounded context (a Part 11 compliance feature) merged without updating any design documentation.

## Decisions

- **Enforcement layer**: CI workflow (`claude-code-review.yml`)
- **Detection method**: Convention-based rules (deterministic, no false positives)
- **Enforcement mode**: Advisory comment on PR (non-blocking)
- **ADR reorganization**: Move all ADRs from `docs/architecture/` to `docs/architecture/adr/` subdirectory

## Scope

Three pieces of work, executed in order:

### Part 1: Move ADRs to Subdirectory

Move 21 `adr-*.md` files + `README.md` from `docs/architecture/` to `docs/architecture/adr/`.

**Before:**

```
docs/architecture/
├── adr-001-foundation-layer-priority.md
├── ... (21 ADR files)
├── adr-021-app-shell-loading-protocol.md
├── README.md                          ← ADR index mixed with architecture docs
├── hld-software-architecture.md
├── sdd-backend-architecture.md
├── sdd-frontend-architecture.md
└── github-app-installation-onboarding.md
```

**After:**

```
docs/architecture/
├── adr/
│   ├── README.md                      ← ADR index
│   ├── adr-001-foundation-layer-priority.md
│   ├── ... (21 ADR files)
│   └── adr-021-app-shell-loading-protocol.md
├── hld-software-architecture.md
├── sdd-backend-architecture.md
├── sdd-frontend-architecture.md
└── github-app-installation-onboarding.md
```

**Cross-reference updates required (13 files):**

| File                                                                 | References                    | Update pattern                                                   |
| -------------------------------------------------------------------- | ----------------------------- | ---------------------------------------------------------------- |
| `CLAUDE.md`                                                          | ADR-018, ADR-019              | `docs/architecture/adr-0XX` → `docs/architecture/adr/adr-0XX`    |
| `packages/shared/README.md`                                          | ADR-008, ADR-011, ADR-014     | Same                                                             |
| `.claude/agents/squad-lead/delegation-specs/playwright-bdd-setup.md` | ADR-012                       | Relative path update                                             |
| `.github/agents/squad-lead/delegation-specs/playwright-bdd-setup.md` | ADR-012                       | Relative path update                                             |
| `docs/ux/external-signing/redesign-proposal.md`                      | ADR-005, ADR-007              | Same                                                             |
| `docs/plans/2026-02-07-signing-requests.md`                          | ADR-005, ADR-007              | Same                                                             |
| `docs/plans/2026-02-05-five-bug-fixes.md`                            | ADR-005                       | Same                                                             |
| 8 ADR files with internal cross-refs                                 | Various                       | No change needed (same directory)                                |
| `.claude/agents/quality/code-reviewer.agent.md`                      | Documentation placement rules | Update `docs/architecture/` guidance to mention `adr/` subfolder |

### Part 2: Update Stale Documentation (Signing Requests)

All documentation gaps identified from the Signing Requests feature merge (commit `6317fca`):

#### HLD (`docs/architecture/hld-software-architecture.md`)

- **Section 3.2 (Module Decomposition)**: Add `signing-requests` module (14th module)
- **Section 4 (Bounded Contexts)**: Add Signing Requests as 7th bounded context with purpose, modules, features
- **Section 7.3 (Cloud Functions)**: Add 4 missing functions:
  - `signedPdfWorker` - Generate PDF with digital signatures (Cloud Task)
  - `signingNotificationWorker` - Send signing invitations via email (Cloud Task)
  - `signingExpiryChecker` - Check daily for expired requests (Scheduled, 02:00 UTC)
  - `dailySyncScheduler` - Periodic document metadata sync (Cloud Scheduler)

#### SDD Frontend (`docs/architecture/sdd-frontend-architecture.md`)

- **Section 5.2 (Feature Audit Table)**: Add `signing-requests` row (12th feature, status: Complete - target pattern)

#### SDD Backend (`docs/architecture/sdd-backend-architecture.md`)

- **Module Registry**: Add `signing-requests/` to backend module list

#### Domain Documentation

- **`docs/domain/bounded-contexts.md`**: Add Signing Requests section with code locations and relationships
- **`docs/domain/aggregates.md`**: Add aggregates: SigningRequest, SigningDocument, SigningField, Signer, SignerToken
- **`docs/domain/ubiquitous-language.md`**:
  - Add terms: Signing Request, External Signer, Signing Field, Signer Token
  - Remove stale "Auditor (Role)" entry (removed in commit `6317fca`)

#### ADR README (`docs/architecture/adr/README.md`)

Add 5 missing entries to the index:

- ADR-017: Journey-Based E2E Testing
- ADR-018: Server-Side Compliance Data
- ADR-019: Entity Reference Validation
- ADR-020: ViewModel Hook Pattern
- ADR-021: App Shell Loading Protocol

### Part 3: Extend CI Workflow

Update `.github/workflows/claude-code-review.yml` to include design document alignment rules in the review prompt.

#### Convention-Based Detection Rules

| #   | If PR adds...                                 | Then check...                                      |
| --- | --------------------------------------------- | -------------------------------------------------- |
| 1   | New directory in `apps/services/src/modules/` | HLD Section 3.2 lists that module name             |
| 2   | New export in `apps/functions/src/index.ts`   | HLD Section 7.3 lists that function                |
| 3   | New directory in `apps/web/src/features/`     | SDD Frontend Section 5.2 feature table includes it |
| 4   | New file in `packages/data/src/models/`       | `docs/domain/aggregates.md` documents those models |
| 5   | New ADR file in `docs/architecture/adr/`      | `docs/architecture/adr/README.md` indexes it       |

#### Workflow Change

Extend the `prompt` field in `claude-code-review.yml`:

```yaml
- name: Run Claude Code Review
  id: claude-review
  uses: anthropics/claude-code-action@v1
  with:
    claude_code_oauth_token: ${{ secrets.CLAUDE_CODE_OAUTH_TOKEN }}
    plugin_marketplaces: 'https://github.com/anthropics/claude-code.git'
    plugins: 'code-review@claude-code-plugins'
    prompt: |
      /code-review:code-review ${{ github.repository }}/pull/${{ github.event.pull_request.number }}

      Additionally, perform an ISO 13485 Design Document Alignment Check.
      This project is a medical device QMS requiring design documents to stay
      current with implementation (ISO 13485 design outputs).

      Check the PR diff for these conventions:

      1. NEW BACKEND MODULE: If the PR adds a new directory under
         apps/services/src/modules/, verify that
         docs/architecture/hld-software-architecture.md Section 3.2
         (Module Decomposition) lists that module name.

      2. NEW CLOUD FUNCTION: If the PR adds or modifies exports in
         apps/functions/src/index.ts, verify that
         docs/architecture/hld-software-architecture.md Section 7.3
         (Cloud Functions) lists all exported functions.

      3. NEW FRONTEND FEATURE: If the PR adds a new directory under
         apps/web/src/features/, verify that
         docs/architecture/sdd-frontend-architecture.md Section 5.2
         (Feature Audit table) includes that feature.

      4. NEW DATA MODEL: If the PR adds a new file in
         packages/data/src/models/, verify that
         docs/domain/aggregates.md documents the new model's aggregates.

      5. NEW ADR: If the PR adds a new file in docs/architecture/adr/,
         verify that docs/architecture/adr/README.md indexes the new ADR.

      For each violation found, include a section in your review:
      "ISO 13485 Design Doc Alignment: [which doc] does not mention
      [which new component]. This design document should be updated
      to maintain traceability."

      This check is advisory only. Do not request changes solely for
      missing documentation - flag it as a comment for the team to address.
```

## Out of Scope

- Changes to local agents (Code Reviewer, Software Engineer) - future iteration
- Changes to skills (`github-implement`, `github-brainstorm`) - future iteration
- Automated ADR README generation from file system - future iteration
- CI scripts that programmatically validate (e.g., bash script comparing directory listings to doc content) - the AI-based review is sufficient for now

## Risks

| Risk                                     | Mitigation                                                                                 |
| ---------------------------------------- | ------------------------------------------------------------------------------------------ |
| Claude misses a convention violation     | Rules are simple and deterministic; low risk of false negatives                            |
| False positives on renamed/moved modules | Prompt says "new directory" not "changed directory"                                        |
| Cross-reference breakage from ADR move   | Blast radius is known (13 files); all updates are find-and-replace                         |
| Plan doc references become stale         | Plan docs (`docs/plans/`) are historical artifacts; update paths but accept some staleness |

## Implementation Sequence

1. Create feature branch and worktree
2. `git mv` all 21 ADR files + README to `docs/architecture/adr/`
3. Update all 13 cross-reference files
4. Update stale design docs (HLD, SDD, domain) for Signing Requests
5. Update ADR README with missing entries (017-021)
6. Update `claude-code-review.yml` with extended prompt
7. Run `pnpm format:check` and fix formatting
8. Verify no broken links with grep
9. Create PR
