# CI Cost Optimization Design

## Overview

Reduce GitHub Actions billable minutes from ~50-60 per PR push to ~18-22 by consolidating parallel jobs into a single sequential job, adding Turbo affected-package filtering, and making preview deployments label-triggered.

## Problem Statement

The project has 3000 free build minutes/month on a private repo. With 5-10 PR pushes/day at ~50 billable minutes each, the budget is exhausted in ~6-12 days. The current CI is optimized for speed (8 parallel jobs) at the expense of cost.

## Design Decisions

### Decision 1: Consolidate CI into a Single Job

**Choice:** Replace the 8-job DAG in `ci.yml` with one sequential job.

**Rationale:** Each job pays ~2-3 min of setup overhead (checkout, pnpm install, artifact download). With 8 jobs that's ~24 min of pure overhead. A single job pays it once.

**Alternatives Considered:**

- 2-3 job hybrid (lint+typecheck, tests, build) -- saves less overhead, still ~35 min
- Keep parallel, add filtering only -- saves 30-40% vs 60%

**Trade-off:** Wall-clock time increases from ~15 min to ~20 min. Acceptable for a small team.

### Decision 2: Turbo Affected-Package Filtering

**Choice:** Use `--filter=...[origin/main]` to only run tasks for changed packages and their dependents.

**Rationale:** A frontend-only change doesn't need to lint/typecheck/test the backend. Turbo's filter syntax handles transitive dependents correctly (editing `shared` still tests everything that depends on it).

**Alternatives Considered:**

- Path-based GitHub Actions filtering -- coarser, doesn't understand package dependencies
- Manual skip labels -- error-prone, requires developer action

### Decision 3: Label-Triggered Preview Deployments

**Choice:** Only run `preview.yml` when the PR has a `preview` label.

**Rationale:** Preview deployments cost ~6-8 min per push and aren't needed for every PR. Label-triggering makes them opt-in.

**Alternatives Considered:**

- Remove entirely -- too aggressive, previews are useful for design review
- Keep as-is -- wastes ~6-8 min on every push

## Implementation

### 1. `ci.yml` -- Consolidated Single Job

Replace the current multi-job workflow with:

```yaml
name: CI

on:
  pull_request:
    branches: [main, develop]

concurrency:
  group: ${{ github.workflow }}-${{ github.ref }}
  cancel-in-progress: true

jobs:
  ci:
    name: Build, Lint & Test
    runs-on: ubuntu-latest
    timeout-minutes: 25
    environment: production

    steps:
      - name: Checkout
        uses: actions/checkout@v4
        with:
          fetch-depth: 0 # Full history for Turbo diff

      - name: Fetch main for Turbo filter
        run: git fetch origin main:refs/remotes/origin/main

      - name: Setup pnpm
        uses: pnpm/action-setup@v4

      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version-file: '.nvmrc'
          cache: 'pnpm'

      - name: Install dependencies
        run: pnpm install --frozen-lockfile

      - name: Turbo Cache
        uses: actions/cache@v4
        with:
          path: .turbo
          key: ${{ runner.os }}-turbo-${{ github.sha }}
          restore-keys: |
            ${{ runner.os }}-turbo-

      - name: Build packages (affected)
        run: pnpm turbo run build --filter=...[origin/main]

      - name: Lint (affected)
        run: pnpm turbo run lint --filter=...[origin/main]

      - name: Format check
        run: pnpm format:check

      - name: Type check (affected)
        run: pnpm turbo run typecheck --filter=...[origin/main]

      - name: Test with coverage (affected)
        run: pnpm turbo run test:coverage --filter=...[origin/main]

      - name: Coverage check
        run: |
          FAILED=0
          for pkg in web functions services shared; do
            COVERAGE_FILE=""
            case $pkg in
              web|functions|services) COVERAGE_FILE="apps/$pkg/coverage/coverage-summary.json" ;;
              shared) COVERAGE_FILE="packages/$pkg/coverage/coverage-summary.json" ;;
            esac

            if [ ! -f "$COVERAGE_FILE" ]; then
              echo "⏭️  $pkg: no coverage file (skipped by filter)"
              continue
            fi

            LINES=$(jq '.total.lines.pct' "$COVERAGE_FILE")
            echo "📊 $pkg: ${LINES}% line coverage"
            if (( $(echo "$LINES < 80" | bc -l) )); then
              echo "❌ $pkg: below 80% threshold"
              FAILED=1
            fi
          done
          if [ "$FAILED" -eq 1 ]; then
            echo "Coverage check failed"
            exit 1
          fi

      - name: Build apps (affected)
        run: pnpm turbo run build --filter=...[origin/main]
        env:
          VITE_FIREBASE_API_KEY: ${{ secrets.VITE_FIREBASE_API_KEY }}
          VITE_FIREBASE_AUTH_DOMAIN: ${{ secrets.VITE_FIREBASE_AUTH_DOMAIN }}
          VITE_FIREBASE_PROJECT_ID: ${{ secrets.VITE_FIREBASE_PROJECT_ID }}
          VITE_FIREBASE_STORAGE_BUCKET: ${{ secrets.VITE_FIREBASE_STORAGE_BUCKET }}
          VITE_FIREBASE_MESSAGING_SENDER_ID: ${{ secrets.VITE_FIREBASE_MESSAGING_SENDER_ID }}
          VITE_FIREBASE_APP_ID: ${{ secrets.VITE_FIREBASE_APP_ID }}
```

**Notes:**

- `fetch-depth: 0` required for Turbo to diff against `origin/main`
- Turbo cache uses `restore-keys` fallback so PRs benefit from prior builds
- `format:check` runs globally (Prettier doesn't have per-package filtering)
- Coverage check skips packages that weren't tested (no coverage file = filtered out)
- Build apps step reuses Turbo cache from the earlier build packages step

### 2. `preview.yml` -- Label-Triggered

```yaml
name: Preview

on:
  pull_request:
    types: [labeled, synchronize]

jobs:
  preview:
    name: Deploy Preview
    if: contains(github.event.pull_request.labels.*.name, 'preview')
    runs-on: ubuntu-latest
    timeout-minutes: 15
    environment: production
    # ... rest of job stays the same
```

### 3. Files to Modify

| File                            | Change                                  |
| ------------------------------- | --------------------------------------- |
| `.github/workflows/ci.yml`      | Replace 8-job DAG with single job       |
| `.github/workflows/preview.yml` | Add label trigger + condition           |
| `turbo.json`                    | No changes needed (filter is CLI-level) |

### 4. Files NOT Changed

| File                     | Reason                                          |
| ------------------------ | ----------------------------------------------- |
| `e2e.yml`                | Already weekly/manual, minimal cost             |
| `deploy.yml`             | Only runs on main push, required for production |
| `claude-code-review.yml` | Minimal cost (no build/test)                    |
| `claude.yml`             | On-demand only                                  |

## Estimated Savings

| Scenario                | Before      | After      | Savings |
| ----------------------- | ----------- | ---------- | ------- |
| Full-monorepo PR push   | ~50 min     | ~20 min    | 60%     |
| Frontend-only PR push   | ~50 min     | ~12 min    | 76%     |
| Backend-only PR push    | ~50 min     | ~14 min    | 72%     |
| PR push + preview       | ~58 min     | ~20 min    | 66%     |
| Monthly (10 pushes/day) | ~15,000 min | ~4,800 min | 68%     |

At 10 pushes/day, the 3000 min budget would last ~19 days instead of ~6.

## Testing Strategy

1. Create a PR with a frontend-only change and verify:
   - Backend tests are skipped
   - Coverage check skips backend packages gracefully
   - Build completes successfully

2. Create a PR with a `shared` package change and verify:
   - All dependent packages are tested

3. Test preview label:
   - PR without label: no preview job
   - Add label: preview deploys
   - Push with label: preview re-deploys

## Open Questions

None -- trusting Turbo's dependency graph for affected-package detection.
