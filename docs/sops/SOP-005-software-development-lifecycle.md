---
id: SOP-005
title: 'Software Development Lifecycle'
status: draft
links:
  - SOP-001
  - SOP-003
  - SOP-006
  - SOP-007
---

# Software Development Lifecycle

## 1. Purpose

Define the software development lifecycle (SDLC) for PactoSigna, ensuring that software is planned, developed, verified, and released in a controlled manner consistent with IEC 62304 §5 and the project's AI-augmented workflow.

## 2. Scope

Applies to all software development activities across the PactoSigna monorepo (apps/web, apps/services, apps/functions, packages/\*). Covers planning through release for all team members and AI assistants involved in development.

## 3. Definitions

| Term           | Definition                                                                        |
| -------------- | --------------------------------------------------------------------------------- |
| Monorepo       | Single repository containing all apps and packages, managed with pnpm + Turborepo |
| Feature Branch | Git branch created for a single issue or change, worked in a git worktree         |
| Worktree       | Isolated git working directory for parallel development                           |
| CI Pipeline    | GitHub Actions workflow that runs lint, typecheck, test, and build                |
| Release        | A versioned, approved deployment managed through PactoSigna's release workflow    |

## 4. Responsibilities

| Role            | Responsibility                                                |
| --------------- | ------------------------------------------------------------- |
| Product Manager | Defines requirements and acceptance criteria in GitHub Issues |
| Developer       | Implements code, writes tests, creates Pull Requests          |
| Quality Manager | Reviews PRs, approves releases, ensures process compliance    |
| AI Assistant    | Assists with implementation, code review, and test generation |

## 5. Procedure

### 5.1 Planning

1. All work begins with a GitHub Issue containing acceptance criteria (SOP-001).
2. Verify the issue is in "Ready" status on the project board before starting implementation.
3. Create a git worktree and feature branch: `git worktree add .worktrees/<name> -b feature/<name>`.

### 5.2 Implementation

1. Implement changes in the feature branch, following the package boundaries defined in ADR-014.
2. Write or update unit tests (Vitest) alongside implementation.
3. Run `pnpm lint`, `pnpm typecheck`, and `pnpm test` locally before committing.
4. Run `pnpm format:check` and fix any formatting issues with `pnpm format`.

### 5.3 Code Review

1. Push the feature branch and open a Pull Request referencing the GitHub Issue (`Closes #<number>`).
2. Assign at least one reviewer. The reviewer checks: correctness, test coverage, adherence to architecture (ADRs), and frontmatter validity for any docs changes.
3. Address all review comments. Re-request review after changes.
4. Approval from at least one reviewer is required before merge.

### 5.4 Continuous Integration

1. The CI pipeline runs automatically on every PR: lint → typecheck → unit tests → build → E2E tests.
2. All CI checks must pass before merge is permitted.
3. If CI fails, the developer investigates and fixes before requesting re-review.

### 5.5 Release

1. Create a Release in PactoSigna linking the PRs and documents included.
2. Obtain required electronic signatures per the release workflow.
3. Deploy to production only after all signatures are collected and the release is published.
4. Record the release in PactoSigna's release management module.

## 6. Records

| Record           | Retention       | Location               |
| ---------------- | --------------- | ---------------------- |
| GitHub Issues    | Life of product | GitHub                 |
| Pull Requests    | Life of product | GitHub                 |
| CI pipeline logs | 1 year          | GitHub Actions         |
| Release records  | Life of product | PactoSigna (Firestore) |

## 7. References

- IEC 62304:2006 §5 — Software Development Process
- ISO 13485:2016 §7.3 — Design and Development
- SOP-001 — Issue-Driven AI Development Workflow
- SOP-003 — Design Control
- SOP-006 — Change Control
- SOP-007 — Verification & Validation

## Revision History

| Version | Date       | Author        | Changes       |
| ------- | ---------- | ------------- | ------------- |
| 0.1     | 2026-02-15 | PactoSigna AI | Initial draft |
