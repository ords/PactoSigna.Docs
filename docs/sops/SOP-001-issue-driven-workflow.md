# Issue-Driven AI Development Workflow

This guide describes how to use AI assistants (Claude, GitHub Copilot) for feature development using GitHub issues as the single source of truth.

## Overview

```
┌──────────┐     ┌──────────┐     ┌─────────────┐     ┌───────────┐
│ Backlog  │ →   │  Ready   │ →   │ In Progress │ →   │ In Review │
│          │     │          │     │             │     │           │
│ brain-   │     │ Approved │     │ implement-  │     │ PR ready  │
│ storming │     │ for work │     │ ing         │     │ for human │
└──────────┘     └──────────┘     └─────────────┘     └───────────┘
```

## Skills

| Skill              | Status  | Purpose                                                        |
| ------------------ | ------- | -------------------------------------------------------------- |
| `brainstorming`    | Backlog | Team analysis, GitHub issue creation, iterative refinement     |
| `implementing`     | Ready   | Create worktree, delegate to engineers, code review, create PR |
| `bugfixing`        | Any     | Root cause investigation, test-first fix                       |
| `code-simplifying` | Any     | Pre-commit cleanup, simplify code without changing behavior    |
| `finishing-branch` | Any     | Pre-PR checklist: lint, test, format, typecheck, build         |

## Session 1: Brainstorming

**Use skill:** `brainstorming`

**When:** Issue is in Backlog status and needs refinement, or a new idea needs structured analysis.

**What it does:**

1. Restates the idea to confirm understanding
2. Invokes three agents in parallel:
   - product-manager → requirements analysis, user stories, acceptance criteria
   - product-designer → JTBD analysis, user journey, persona consultation
   - architect-software → domain modeling, bounded contexts, ADRs
3. Synthesizes team analysis into a GitHub issue
4. Enters refinement loop with user until "ready"

**Output:** GitHub issue(s) on project board with full team analysis, acceptance criteria, and size estimate.

**After brainstorming:** Move issue to Ready when approved.

## Session 2: Implementation

**Use skill:** `implementing`

**When:** Issue is in Ready status with clear acceptance criteria.

**What it does:**

1. Reads issue and verifies acceptance criteria
2. Creates worktree for isolated work
3. Delegates to eng-frontend and/or eng-backend with delegation specs
4. Runs code-simplifying skill on modified files
5. Runs finishing-branch checklist (lint, test, format, typecheck, build)
6. Creates PR with "Closes #<number>"

**Output:** PR ready for review that closes the issue when merged.

## Quick Commands

```bash
# Read issue
gh issue view <number>
gh issue view <number> --comments

# Check status
gh issue view <number> --json projectItems --jq '.projectItems[].status.name'

# Post comment (done by skill)
gh issue comment <number> --body-file <file>
```

## Artifact Locations

| Artifact           | Location                       | Created By    |
| ------------------ | ------------------------------ | ------------- |
| UX JTBD analysis   | `docs/ux/[feature]-jtbd.md`    | brainstorming |
| UX journey map     | `docs/ux/[feature]-journey.md` | brainstorming |
| UX flow spec       | `docs/ux/[feature]-flow.md`    | brainstorming |
| Domain analysis    | `docs/domain/`                 | brainstorming |
| ADRs               | `docs/architecture/`           | brainstorming |
| Brainstorm summary | GitHub issue comment           | brainstorming |
| Implementation     | Feature branch                 | implementing  |
| PR                 | GitHub                         | implementing  |

## Documentation Timing

| Doc Type     | When Updated                     | Why                       |
| ------------ | -------------------------------- | ------------------------- |
| ADRs         | BEFORE implementation            | Captures the decision     |
| Requirements | AFTER implementation             | Reflects what was built   |
| Domain docs  | AFTER implementation             | Reflects actual model     |
| UX artifacts | During brainstorm, refined AFTER | Evolves with learnings    |
| CLAUDE.md    | AFTER implementation             | Reflects current codebase |

## Agent Roster

| Agent                        | Role                                                    |
| ---------------------------- | ------------------------------------------------------- |
| lead-squad                   | Orchestrator — delegates to all other agents            |
| product-manager              | Requirements, user stories, backlog management          |
| product-designer             | JTBD, user journeys, design specs, persona consultation |
| architect-software           | Domain modeling, system design, ADRs, bounded contexts  |
| eng-frontend                 | React components, hooks, MUI, client-side state         |
| eng-backend                  | Fastify routes, Cloud Functions, Firestore, Cloud Tasks |
| quality-architect-review     | Architecture pattern compliance, ADR adherence          |
| quality-code-review          | Code standards, lint, types, test quality               |
| quality-documentation-review | Doc completeness, accuracy, ISO 13485 compliance        |
| quality-manual-testing       | Exploratory testing, UX walkthroughs, acceptance        |
| quality-automated-testing    | Vitest, Playwright, E2E, coverage, CI pipelines         |
| quality-security-performance | Part 11 compliance, OWASP, performance audits           |

## See Also

- [ADR-015: Issue-Driven AI Workflow](../architecture/adr/adr-015-issue-driven-ai-workflow.md)
- [Personas](../personas/README.md)
