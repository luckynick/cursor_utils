# Testing Protocol

## Testing Conventions
- Use accessible selectors (`getByRole`, `getByLabelText`) - **never CSS selectors**
- E2E selectors must be in `e2e/test-utils/selectors.ts`
- Tests must have **no conditional branches** (no `if` statements)
- Import types from `src/types/` for properly typed mocks

## Commands
```bash
npm test                 # Full: lint + type-check + Jest
npm run test:fast        # Jest only (skip checks)
npm run test:watch       # Jest watch mode
npm run test:e2e         # Playwright E2E tests
npm run test:e2e:p0      # P0 priority E2E only
npm run test:e2e -- --grep "test name"  # Specific test
```

## Playwright
- Use `--reporter=line` to avoid blocking
- Dev server is usually already running - navigate directly to `http://localhost:3000`

## Task
$ARGUMENTS
