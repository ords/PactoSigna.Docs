---
id: WI-007
title: 'Development Environment Setup'
status: draft
links:
  - type: related-to
    target: SOP-005
---

# Development Environment Setup

## Install & Development

```bash
pnpm install               # Install dependencies
pnpm dev:e2e               # Start both emulators and web app
pnpm emulators             # Firebase emulators only
pnpm dev                   # All apps via Turborepo
```

## Build & Quality

```bash
pnpm build                 # Build all packages
pnpm lint                  # Run ESLint
pnpm lint:fix              # Auto-fix lint issues
pnpm format                # Prettier format all files
pnpm format:check          # Check formatting
pnpm typecheck             # TypeScript strict mode check
```

## Unit Tests

```bash
pnpm test                  # Run all unit tests
pnpm test:coverage         # With coverage
```

## E2E Tests

Requires emulators + web dev server running (`pnpm dev:e2e`).

```bash
pnpm e2e                   # Run all e2e tests
pnpm e2e --grep "@journey-1"  # Run specific journey
pnpm e2e:ui                # Playwright UI mode for debugging
pnpm e2e:headed            # Run with visible browser
pnpm e2e:debug             # Debug mode with PWDEBUG=1
```

## Workspace Filtering

```bash
pnpm --filter @pactosigna/web test
pnpm --filter @pactosigna/functions build
pnpm --filter @pactosigna/services lint
```

## Firebase Emulator Ports

| Service           | Port |
| ----------------- | ---- |
| Auth              | 9098 |
| Firestore         | 8081 |
| Functions         | 5002 |
| Hosting (qms)     | 5003 |
| Hosting (landing) | 5008 |
| Emulator UI       | 4001 |
| Emulator Hub      | 4400 |
