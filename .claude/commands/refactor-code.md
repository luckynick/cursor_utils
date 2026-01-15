# Refactor Application Code

Review application code and refactor for reusability, maintainability, and KISS.

## Required Input
User must specify scope: file path, directory, functionality, "uncommitted" for git changes, etc.

## Pre-flight
1. Confirm scope from `$ARGUMENTS` (e.g., `src/store/game-store.ts`, `animation logic`, `uncommitted`)
2. Ask user: **Analyze only** (dump findings to markdown) or **analyze and refactor**?

## Analysis Checklist

### Anti-patterns to Fix
- **Duplicate business logic** - Extract to `src/lib/` utilities
- **Magic numbers/strings** - Extract to `src/lib/constants/`
- **Complex inline logic** - Extract to pure functions
- **Missing accessibility** - Add aria-labels, roles for testability
- **Global window state** - Consider dedicated registry/manager classes
- **Hardcoded values** - Use constants or config
- **Mixed concerns** - Separate pure logic from React components
- **Dead code** - Remove unused imports, variables, functions

### Best Practices to Apply
- **Pure functions** - Extract logic from components to `src/lib/`
- **Factory pattern** - `createX()` functions for complex objects
- **Single responsibility** - One purpose per function/component
- **Explicit types** - Derive from source types, avoid `any`
- **Constants file** - Centralize magic values in `src/lib/constants/`
- **Accessibility** - `aria-label`, `role` attributes for interactive elements

## Common Extractions

### Business Logic
```typescript
// Before: inline in component
const winner = playerCard.value > aiCard.value ? 'player' : 'ai';

// After: src/lib/game/scoring.ts
export function determineWinner(playerCard: Card, aiCard: Card): 'player' | 'ai' | 'tie' {
  if (playerCard.value > aiCard.value) return 'player';
  if (aiCard.value > playerCard.value) return 'ai';
  return 'tie';
}
```

### Constants
```typescript
// src/lib/constants/animation.ts
export const ANIMATION_TIMING = {
  CARD_PLAY: 500,
  WITHDRAWAL_DELAY: 100,
  WITHDRAWAL_DURATION: 650,
  AI_TURN_DELAY: 1000,
} as const;
```

### Type Derivation
```typescript
// Derive types from source to stay in sync
type GameStoreType = ReturnType<typeof useGameStore>;
type GameStoreActionName = Exclude<keyof GameStoreType, keyof GameState>;
```

## Directory Guidelines
- `src/lib/game/` - Game mechanics, scoring, rules
- `src/lib/utils/` - Generic utilities
- `src/lib/constants/` - Magic values, timing, config
- `src/lib/factories/` - Object creation functions
- `src/lib/animation/` - Animation logic, registries

## Verification
```bash
npm test                              # Unit + type + lint tests pass
npm run test:e2e -- --reporter=line  # E2E tests pass
```

## Task

$ARGUMENTS
