# ADR-015: Issue-Driven AI Development Workflow

## Status

Accepted

## Context

Our documentation is scattered across multiple locations (CLAUDE.md, .github/agents, .github/skills, docs/plans, docs/planning, docs/architecture, docs/domain, docs/requirements). This creates three problems:

1. **Discovery problem** - AI assistants don't know where to find relevant context
2. **Workflow friction** - Too many manual steps between "GitHub issue" and "merged PR"
3. **Outdated content** - Old plans and docs linger, creating confusion about what's current

We have 72 implementation plan files in `docs/plans/` that often drift from their corresponding issues, and sprint planning docs that no longer reflect our workflow.

## Decision

We adopt an **issue-driven AI development workflow** where:

1. **GitHub issues are the single source of truth** for work items - no separate plan files
2. **Two-session workflow**:
   - Brainstorm session (repeatable): AI discovers context, enriches issue
   - Implementation session (single): AI implements, verifies, creates PR
3. **Reuse existing plugins** rather than building custom solutions:
   - superpowers (worktrees, TDD, verification)
   - feature-dev (7-phase development workflow)
   - commit-commands (git workflow automation)
   - code-simplifier (code quality)
4. **Documentation timing**:
   - ADRs: Written BEFORE implementation (capture decisions)
   - Everything else: Updated AFTER implementation (reflect reality)
5. **Simplified docs structure**:
   - Delete `docs/plans/` and `docs/planning/` (legacy)
   - Add `docs/ux/` for UX artifacts
   - Keep `docs/architecture/`, `docs/domain/`, `docs/requirements/`, `docs/guides/`

### Documentation Structure

```
docs/
  architecture/     # ADRs - updated BEFORE implementation
  domain/           # DDD docs - updated AFTER implementation
  requirements/     # Epic-level requirements - updated AFTER implementation
  guides/           # How-to guides including workflow guide
  ux/               # NEW: Journey maps, wireframes, personas
```

### Workflow

```
┌─────────────────────────────────────────────────────────────┐
│  BRAINSTORM SESSION (repeatable)                            │
│  Trigger: Paste issue URL or describe work                  │
│                                                             │
│  1. AI reads issue via gh CLI                               │
│  2. AI runs discovery (feature-dev Phase 1-3)               │
│  3. AI searches for relevant ADRs, domain docs, code        │
│  4. AI enriches issue with Context, Approach, AC            │
│  5. Human approves → moves issue to Ready                   │
└─────────────────────────────────────────────────────────────┘
                            ↓
┌─────────────────────────────────────────────────────────────┐
│  IMPLEMENTATION SESSION (single)                            │
│  Trigger: Issue in Ready status                             │
│                                                             │
│  1. AI reads issue via gh CLI                               │
│  2. AI creates worktree (superpowers:using-git-worktrees)   │
│  3. AI implements (feature-dev Phase 4-6)                   │
│  4. AI simplifies (code-simplifier)                         │
│  5. AI verifies (superpowers:verification-before-completion)│
│  6. AI creates PR (/commit-push-pr)                         │
│  7. Issue moves to In Review                                │
└─────────────────────────────────────────────────────────────┘
```

## Consequences

### Positive

- **Single source of truth** - Issues contain all context, no drift
- **Reduced maintenance** - No custom plugins to maintain, reuse existing
- **Clear workflow** - AI knows exactly what to do at each stage
- **Automatic cleanup** - No stale plan files accumulating
- **Audit trail** - GitHub issues provide history of decisions and changes

### Negative

- **GitHub dependency** - Workflow relies on GitHub issues and Projects
- **Learning curve** - Team needs to learn new workflow
- **Plugin updates** - Dependent on upstream plugin maintenance

### Neutral

- **Migration required** - Need to delete legacy docs/plans and docs/planning
- **Issue template** - Need to create and maintain issue template
