# Refactor Tests

Review test code and refactor following best practices.

## Required Input
User must specify scope: file path, directory, functionality, "uncommitted" for git changes, etc.

## Pre-flight
1. Confirm scope from `$ARGUMENTS` (e.g., `src/components/game/*.test.tsx`, `animation tests`, `uncommitted`)
2. Ask user: **Analyze only** (dump findings to markdown) or **analyze and refactor**?

## Analysis Checklist

### Anti-patterns to Fix
- **Verbose AAA comments** - Remove if test is self-explanatory
- **Duplicate mock data** - Extract to factories (`createCard()`, `createMockPlayer()`)
- **Inline mocks** - Use `createPlayedCard()`, `createMockGameStoreActions()`
- **Magic numbers** - Extract timing constants (100ms, 650ms, etc.)
- **Fragile assertions** - Replace `document.body.textContent` with `screen.getByText()`
- **CSS selector tests** - Use `getByRole`, `getByLabelText` instead
- **Separate timing test files** - Merge into main test file (keep critical regressions)
- **Granular state tests** - Consolidate with `toMatchObject()`
- **Manual type lists** - Derive from real types (`Exclude<keyof Store, keyof State>`)

### Best Practices to Apply
- **Factory pattern** - `createCard()`, `createPlayedCard()`, `createMockPlayer()`
- **Type derivation** - `type ActionName = Exclude<keyof StoreType, keyof StateType>`
- **Accessible selectors** - `getByRole`, `getByLabelText`, `getByAltText`
- **toMatchObject** - Consolidate multiple expect() calls
- **Empty images for text fallback** - `imageClothed: '', imageUnclothed: ''`

## Factory Locations
- **Domain objects**: `src/lib/factories/game-factory.ts`
- **Test mocks**: `src/test-utils/store-helpers.ts`
- **Animation helpers**: `src/test-utils/animation-helpers.ts`

## Existing Factories
```typescript
// src/lib/factories/game-factory.ts
createCard({ id, name, value, nation, isGolden })
createPlayedCard(cardOverrides, playedBy)
createGameState(overrides)

// src/test-utils/store-helpers.ts
createMockGameStoreActions(overrides)
createMockMatchup(overrides)
createMockPlayer({ handCards, collectedCards, ...overrides })
createMockI18nStore(overrides)
createMockSessionStore(overrides)
```

## Verification
```bash
npm run test:fast        # All tests pass
npm run type-check       # No type errors
```

## Task

$ARGUMENTS
