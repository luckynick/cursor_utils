# Bug Fix Protocol

Follow these rules from CLAUDE.md when fixing this bug:

## Test-First Approach (MANDATORY)
1. **Write a failing test first** before implementing any fix
2. Run the test to confirm it fails (proves the test identifies the flaw)
3. Implement the fix
4. Run the test again to confirm it passes

## Code Quality
- Run `npm test` after implementing the fix
- Never disable lint/type errors if there's a viable alternative
- Check `package.json` scripts before running npm/node commands

## Testing Conventions
- Use accessible selectors (`getByRole`, `getByLabelText`) - never CSS selectors
- E2E selectors must be in `e2e/test-utils/selectors.ts`
- Tests must have no conditional branches (no `if` statements)
- Import types from `src/types/` for properly typed mocks

## E2E Testing (MANDATORY)
After unit tests pass, run E2E tests to verify the fix doesn't break user flows:
1. Run `npm run test:e2e -- --reporter=line` to execute Playwright E2E tests
2. If specific E2E tests are relevant to the bug, run them first with `npm run test:e2e -- <test-file> --reporter=line`

## Manual Verification via Playwright MCP
When the bug involves UI behavior that's hard to verify with automated tests:
1. Use `mcp__playwright__browser_navigate` to open `http://localhost:3000` (dev server should already be running)
2. Use `mcp__playwright__browser_snapshot` to capture page state and identify elements
3. Use `mcp__playwright__browser_click` to interact with elements and reproduce the fix scenario
4. Use `mcp__playwright__browser_console_messages` to verify no errors appear in console
5. Document the manual verification steps performed

## Bug Description
$ARGUMENTS
