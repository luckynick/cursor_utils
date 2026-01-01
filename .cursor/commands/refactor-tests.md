# Refactor Tests for Best Practices

## Command Purpose
Review uncommitted test changes and refactor them following automated testing best practices, focusing on:
- **Maximum code reusability** - Create shared utilities in app code that serve both production and testing
- **Proactive app code improvement** - Modify application code to add reusable factories, builders, and utilities
- **Eliminate duplication** - Avoid separate test-only utilities when app-level utilities can serve both purposes
- **Conciseness** - Reduce verbosity in both test and app code
- **Test actual business logic** - Focus on behavior and functionality, not implementation details or mocks
- **Go the extra mile** - Always look for opportunities to make code more reusable across the entire codebase

## Agent Permissions & Expectations
**You are FULLY AUTHORIZED to modify application code** to improve reusability and testability:
- ✅ Add factory functions and builders to `src/lib/` or appropriate directories
- ✅ Extract reusable utilities from test code into application code
- ✅ Refactor app code to make it more testable and reusable
- ✅ Create shared constants, types, and helpers that serve both app and tests
- ✅ Add accessibility attributes (aria-labels, roles) to components for better testing
- ✅ Reorganize code structure for better separation of concerns

**Philosophy**: If a utility is needed for tests, consider if it could be useful in the app too. Prefer shared utilities over test-only utilities.

## Process

### 1. Analyze Uncommitted Changes
First, identify all test files with uncommitted changes:

```bash
git diff --name-only | grep -E '\.(test|spec)\.(ts|tsx|js|jsx)$'
```

Show the detailed diff of test changes:

```bash
git diff --unified=5 -- '*.test.*' '*.spec.*'
```

### 2. Review Test Quality
Evaluate each modified test file against these criteria:

#### Anti-Patterns to Identify
- **Mock Overuse**: Tests that mock so heavily they're testing the mocks, not the actual logic
- **Duplication**: Repeated setup code, assertions, or test data across multiple tests
- **Verbosity**: Overly detailed or repetitive test code that obscures intent
- **Brittle Tests**: Tests coupled to implementation details (CSS classes, internal state, DOM structure)
- **Missing Abstraction**: Lack of test utilities/helpers for common operations
- **Poor Test Data**: Hard-coded test data scattered throughout instead of centralized factories

#### Best Practices to Apply
- **Test Behavior, Not Implementation**: Focus on user-facing behavior and business logic outcomes
- **DRY Test Code**: Extract reusable test utilities, fixtures, and helper functions
- **Test Data Builders**: Create factory functions or builders for test data
- **Custom Matchers**: Build domain-specific matchers for clearer assertions
- **Minimal Mocking**: Only mock external dependencies; test real integration between your modules
- **Descriptive Test Names**: Use clear, behavior-focused test descriptions
- **Arrange-Act-Assert**: Structure tests clearly with setup, action, and verification phases

### 3. Identify Refactoring Opportunities

For each test file, look for opportunities to create **shared reusable code**:

1. **Shared Utilities in Application Code** (Preferred)
   - Factory functions → Add to `src/lib/factories/` for creating domain objects
   - Builders → Create in `src/lib/builders/` for complex object construction
   - Test data generators → Add to `src/lib/test-data/` (usable in dev, storybook, tests)
   - Helper functions → Extract to appropriate `src/lib/` modules
   - Constants → Centralize in `src/lib/constants/` for shared values
   
   **Examples of shared utilities**:
   ```typescript
   // src/lib/factories/card-factory.ts - Usable in app AND tests
   export const createCard = (overrides?: Partial<Card>): Card => ({ ... });
   
   // src/lib/factories/game-state-factory.ts - Usable for dev, demo, tests
   export const createGameState = (overrides?: Partial<GameState>): GameState => ({ ... });
   
   // src/lib/test-data/sample-cards.ts - Usable in Storybook, tests, demos
   export const SAMPLE_CARDS: Card[] = [ ... ];
   ```

2. **Test-Only Utilities** (Only when shared utilities don't fit)
   - Rendering helpers → `src/test-utils/render-helpers.ts` (only for test-specific setup)
   - Custom matchers → `src/test-utils/matchers.ts` (only for test assertions)
   - Mock configurations → `src/test-utils/mocks.ts` (only for external dependencies)

3. **Mock Reduction**
   - Over-mocked Zustand stores → Consider using real store instances
   - Mocked utility functions → Test with real implementations when possible
   - Mocked child components → Only mock when necessary for isolation
   - Use real factories instead of inline mock objects

4. **App Code Refactoring for Reusability**
   - Extract business logic from components → Move to `src/lib/game/` or similar
   - Create pure functions → Easier to test and reuse
   - Add accessibility attributes → Makes both app and tests better
   - Separate concerns → Split complex components into smaller, reusable pieces
   - Extract hooks → Share logic between components and make it testable

### 4. Refactor Implementation

Apply refactorings systematically, **prioritizing shared application code**:

#### Step 1: Create Shared Application Utilities (HIGHEST PRIORITY)

**Always consider application-level utilities first** - they serve both production and testing needs:

```typescript
// ✅ PREFERRED: src/lib/factories/game-factory.ts
// Usable in: Production code, tests, Storybook, development tools
export const createGameState = (overrides?: Partial<GameState>): GameState => ({
  gameId: crypto.randomUUID(),
  deck: [],
  cardsOnTable: [],
  currentTurn: 'player',
  moveNumber: 0,
  gamePhase: 'setup',
  players: {
    player: createPlayer({ id: 'player', name: 'Player' }),
    ai: createPlayer({ id: 'ai', name: 'AI' }),
  },
  ...overrides,
});

export const createPlayer = (overrides?: Partial<Player>): Player => ({
  id: crypto.randomUUID(),
  name: 'Player',
  hand: [],
  collectedCards: [],
  ...overrides,
});

export const createCard = (overrides?: Partial<Card>): Card => ({
  id: crypto.randomUUID(),
  name: 'Test Card',
  value: 50,
  nation: 'test',
  isGolden: false,
  imageClothed: '/images/cards/default.jpg',
  imageUnclothed: '/images/cards/default-unclothed.jpg',
  ...overrides,
});
```

**Benefits of shared factories**:
- 🎯 Same factories in production for demo/seed data
- 📚 Use in Storybook for component examples
- 🧪 Consistent test data
- 🔧 Development tools can use them
- ♻️ Single source of truth for object creation

```typescript
// ✅ PREFERRED: src/lib/test-data/sample-data.ts
// Usable in: Tests, Storybook, demos, development
export const SAMPLE_CARDS = {
  lowValue: createCard({ name: 'Low Card', value: 10 }),
  midValue: createCard({ name: 'Mid Card', value: 50 }),
  highValue: createCard({ name: 'High Card', value: 90 }),
  golden: createCard({ name: 'Golden Card', value: 85, isGolden: true }),
};

export const SAMPLE_GAME_STATES = {
  initial: createGameState({ gamePhase: 'setup' }),
  playerTurn: createGameState({ gamePhase: 'playing', currentTurn: 'player' }),
  gameEnded: createGameState({ gamePhase: 'ended' }),
};
```

#### Step 2: Create Test-Specific Utilities (ONLY WHEN NECESSARY)

Only create test-specific utilities for things that are **truly test-only**:

```typescript
// ⚠️ ONLY IF NEEDED: src/test-utils/render-helpers.ts
// Use only for test-specific rendering setup
export function renderWithStores(ui: React.ReactElement, storeOverrides = {}) {
  // This is test-specific because it sets up Jest mocks
  return render(ui);
}
```

```typescript
// ⚠️ ONLY IF NEEDED: src/test-utils/mock-router.ts
// Use only for Next.js router mocking (external dependency)
export function createMockRouter(overrides = {}): ReturnType<typeof useRouter> {
  return {
    push: jest.fn(),
    back: jest.fn(),
    forward: jest.fn(),
    refresh: jest.fn(),
    replace: jest.fn(),
    prefetch: jest.fn(),
    ...overrides,
  };
}
```

#### Step 3: Refactor Tests to Use Shared Utilities

Update tests to use the shared application utilities:

```typescript
// Before: Inline mock data
const mockCard = { id: '1', name: 'Test', value: 50, /* ... */ };

// After: Using shared factory
import { createCard } from '@/lib/factories/game-factory';
const mockCard = createCard({ name: 'Test', value: 50 });
```

```typescript
// Before: Hard-coded test data
const mockGameState = {
  gameId: 'test-1',
  deck: [],
  /* ... lots of boilerplate ... */
};

// After: Using shared factory
import { createGameState } from '@/lib/factories/game-factory';
const mockGameState = createGameState({ gameId: 'test-1' });
```

#### Step 4: Extract Business Logic to Reusable Functions

Move logic from components to pure functions:

```typescript
// ✅ PREFERRED: src/lib/game/scoring.ts
// Pure function - testable, reusable, no React dependency
export function determineRoundWinner(
  playerCard: Card,
  aiCard: Card
): 'player' | 'ai' | 'tie' {
  if (playerCard.value > aiCard.value) return 'player';
  if (aiCard.value > playerCard.value) return 'ai';
  return 'tie';
}

// Component uses the pure function
function GameBoard() {
  const handleRound = () => {
    const winner = determineRoundWinner(playerCard, aiCard);
    // ... rest of component logic
  };
}
```

#### Step 5: Simplify Mock Setup with Shared Utilities

```typescript
// Before: Repeated mock setup in every test
beforeEach(() => {
  jest.mocked(useRouter).mockReturnValue({
    push: jest.fn(),
    back: jest.fn(),
    // ... 20 lines of boilerplate
  });
});

// After: Using shared mock factory (test-specific)
import { createMockRouter } from '@/test-utils/mock-router';
beforeEach(() => {
  jest.mocked(useRouter).mockReturnValue(createMockRouter());
});
```

#### Step 6: Improve Test Names and Structure

- Use clear, behavior-focused descriptions
- Group related tests with describe blocks
- Ensure clear Arrange-Act-Assert structure

### 5. Verify Quality

After refactoring:
- Run tests to ensure they still pass
- Check test coverage hasn't decreased
- Verify tests are more readable and maintainable
- Confirm reduction in code duplication
- Ensure tests focus on behavior, not implementation

## Example Refactoring: Shared vs Test-Only Utilities

### Before (Anti-patterns)
```typescript
// ❌ Duplication, inline mocks, hard-coded data, testing CSS classes
describe('GameBoard', () => {
  it('test 1', () => {
    // Hard-coded mock data - duplicated across tests
    const mockCard = { 
      id: '1', 
      name: 'Test', 
      value: 50, 
      nation: 'test',
      isGolden: false,
      imageClothed: 'test.jpg',
      imageUnclothed: 'test-u.jpg'
    };
    
    jest.mocked(useRouter).mockReturnValue({ 
      push: jest.fn(), 
      back: jest.fn(),
      forward: jest.fn(),
      refresh: jest.fn(),
      replace: jest.fn(),
      prefetch: jest.fn(),
    });
    
    render(<GameBoard card={mockCard} />);
    expect(screen.getByText('test')).toHaveClass('some-class'); // Testing CSS!
  });

  it('test 2', () => {
    // Same boilerplate repeated!
    const mockCard = { 
      id: '2', 
      name: 'Test2', 
      value: 60, 
      nation: 'test',
      isGolden: false,
      imageClothed: 'test2.jpg',
      imageUnclothed: 'test2-u.jpg'
    };
    
    jest.mocked(useRouter).mockReturnValue({ 
      push: jest.fn(), 
      back: jest.fn(),
      forward: jest.fn(),
      refresh: jest.fn(),
      replace: jest.fn(),
      prefetch: jest.fn(),
    });
    
    render(<GameBoard card={mockCard} />);
    expect(screen.getByText('test2')).toHaveClass('another-class'); // Testing CSS!
  });
});
```

### After (Best Practices with Shared Utilities)

```typescript
// ✅ Step 1: Create shared application utilities
// src/lib/factories/game-factory.ts - Usable in app AND tests!
export const createCard = (overrides?: Partial<Card>): Card => ({
  id: crypto.randomUUID(),
  name: 'Default Card',
  value: 50,
  nation: 'test',
  isGolden: false,
  imageClothed: '/images/cards/default.jpg',
  imageUnclothed: '/images/cards/default-unclothed.jpg',
  ...overrides,
});

export const createGameState = (overrides?: Partial<GameState>): GameState => ({
  gameId: crypto.randomUUID(),
  deck: [],
  cardsOnTable: [],
  currentTurn: 'player',
  moveNumber: 0,
  gamePhase: 'playing',
  players: {
    player: createPlayer({ id: 'player', name: 'Player' }),
    ai: createPlayer({ id: 'ai', name: 'AI' }),
  },
  ...overrides,
});

// ✅ Step 2: Create test-specific utilities (only for external mocks)
// src/test-utils/mock-router.ts - Only for Next.js router (external dependency)
export function createMockRouter(overrides = {}): ReturnType<typeof useRouter> {
  return {
    push: jest.fn(),
    back: jest.fn(),
    forward: jest.fn(),
    refresh: jest.fn(),
    replace: jest.fn(),
    prefetch: jest.fn(),
    ...overrides,
  };
}

// ✅ Step 3: Refactored tests using shared utilities
// game-board.test.ts
import { createCard } from '@/lib/factories/game-factory'; // Shared!
import { createMockRouter } from '@/test-utils/mock-router'; // Test-only

describe('GameBoard', () => {
  beforeEach(() => {
    // Centralized mock setup using test utility
    jest.mocked(useRouter).mockReturnValue(createMockRouter());
  });

  it('displays card value when card is played', () => {
    // ARRANGE: Use shared factory - concise and reusable
    const card = createCard({ name: 'Test Card', value: 75 });
    
    // ACT: Render component
    render(<GameBoard card={card} />);
    
    // ASSERT: Test behavior, not CSS classes
    expect(screen.getByRole('heading')).toHaveTextContent('Test Card');
    expect(screen.getByText('75')).toBeInTheDocument();
  });

  it('navigates to game end when card is played', () => {
    // ARRANGE: Setup with custom mock to track calls
    const mockPush = jest.fn();
    jest.mocked(useRouter).mockReturnValue(createMockRouter({ push: mockPush }));
    
    const card = createCard({ value: 80 });
    render(<GameBoard card={card} />);
    
    // ACT: User plays card
    fireEvent.click(screen.getByRole('button', { name: /play card/i }));
    
    // ASSERT: Test actual behavior (navigation)
    expect(mockPush).toHaveBeenCalledWith('/game/end');
  });
});
```

### Key Improvements Demonstrated

1. **Shared Factory** (`createCard`) - Usable in:
   - ✅ Tests (as shown)
   - ✅ Storybook stories
   - ✅ Development seed data
   - ✅ Demo/tutorial modes
   - ✅ Any production code needing to create cards

2. **Test-Only Utility** (`createMockRouter`) - Only for external dependencies
   - ⚠️ Only for Next.js router (can't use real one in tests)
   - Centralized in one place
   - Typed properly

3. **No Duplication**
   - Router mock setup in `beforeEach`
   - Factory used consistently
   - No repeated boilerplate

4. **Testing Behavior, Not Implementation**
   - ❌ Before: `expect(...).toHaveClass('some-class')`
   - ✅ After: `expect(screen.getByRole('heading')).toHaveTextContent('Test Card')`
   - Tests actual user-facing behavior

5. **Concise and Readable**
   - Clear Arrange-Act-Assert structure
   - Minimal setup code
   - Obvious intent

## Decision Tree: Shared vs Test-Only Utilities

When you need a utility, follow this decision tree:

```
Need a utility?
│
├─ Is it for creating/manipulating domain objects (Card, GameState, Player)?
│  └─ ✅ CREATE SHARED UTILITY in src/lib/factories/
│     (Reason: Useful in app, tests, Storybook, demos)
│
├─ Is it for test data/samples (collections of domain objects)?
│  └─ ✅ CREATE SHARED UTILITY in src/lib/test-data/
│     (Reason: Useful for development, demos, Storybook, tests)
│
├─ Is it business logic (calculations, validations, rules)?
│  └─ ✅ CREATE SHARED UTILITY in src/lib/game/ or src/lib/utils/
│     (Reason: Production code that should be tested, not test code)
│
├─ Is it for mocking external dependencies (Next.js router, fetch)?
│  └─ ⚠️ CREATE TEST-ONLY UTILITY in src/test-utils/mocks.ts
│     (Reason: Only needed in test environment)
│
├─ Is it for test assertions or custom matchers?
│  └─ ⚠️ CREATE TEST-ONLY UTILITY in src/test-utils/matchers.ts
│     (Reason: Jest-specific, only for testing)
│
└─ Is it for test rendering setup (providers, wrappers)?
   └─ ⚠️ CREATE TEST-ONLY UTILITY in src/test-utils/render-helpers.ts
      (Reason: Test-specific rendering configuration)
```

### Quick Reference

**✅ Always prefer shared utilities for:**
- Domain object factories (`createCard`, `createPlayer`, `createGameState`)
- Sample/seed data collections (`SAMPLE_CARDS`, `DEMO_GAME_STATE`)
- Business logic functions (`calculateScore`, `determineWinner`, `shuffleDeck`)
- Validation functions (`isValidCard`, `isGameOver`)
- Transformation functions (`cardToDTO`, `parseGameState`)
- Constants and enums (`CARD_VALUES`, `GAME_PHASES`)

**⚠️ Only use test-only utilities for:**
- External dependency mocks (`createMockRouter`, `mockFetch`)
- Jest-specific matchers (`toBeValidCard`)
- Test rendering helpers (`renderWithProviders`)
- Test lifecycle utilities (`setupTest`, `cleanupTest`)

## Success Criteria

### Code Reusability
- ✅ Shared application utilities created in `src/lib/` (not test-only)
- ✅ Test data factories usable in production, Storybook, and tests
- ✅ Business logic extracted to pure functions
- ✅ Test-only utilities limited to external dependency mocks only

### Code Quality
- ✅ Test code duplication significantly reduced (>50% reduction in boilerplate)
- ✅ Tests focus on behavior and business logic, not implementation details
- ✅ Mock usage minimized to essential external dependencies only
- ✅ No CSS classes or implementation details in test assertions
- ✅ Clear Arrange-Act-Assert structure in all tests

### Maintainability
- ✅ Tests remain clear, readable, and maintainable
- ✅ Single source of truth for object creation
- ✅ Easy to update test data by changing factories
- ✅ Application code benefits from utilities created for testing

### Verification
- ✅ All tests continue to pass
- ✅ Test coverage maintained or improved
- ✅ TypeScript compilation succeeds without errors
- ✅ No linter warnings introduced

### Extra Mile Achievements 🎯
- ✅ Created utilities serve 3+ purposes (tests, Storybook, dev tools, production)
- ✅ Business logic extracted from components and tested independently
- ✅ Components simplified by using extracted utilities
- ✅ Zero duplicate factory/builder code between app and tests

