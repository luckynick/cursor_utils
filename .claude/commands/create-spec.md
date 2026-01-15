# Create Tech Specification

Transform unrefined draft constraints into a concise tech specification that explains required changes and their impacts.

## Specification Structure

### 1. Summary
Brief description of the change (1-2 sentences).

### 2. Impact Analysis
For each required change, explain:
- **Business Logic**: How game rules, state, or calculations are affected
- **User Experience**: Changes to user flows, interactions, or feedback
- **UI**: Visual changes, component modifications, or layout adjustments

### 3. Implementation Plan
- List affected files/components
- Outline code changes needed
- Include concise code snippets showing before/after patterns
- Suggest code change drafts where helpful

### 4. Testing Plan
- **Unit Tests**: Functions/logic to test, expected behaviors
- **Integration Tests**: Component interactions, state changes
- **E2E Tests**: User flows affected (if applicable)
- List affected test files or new tests needed
- Key test scenarios and edge cases

## Principles
- **Conciseness**: Avoid verbose explanations; focus on essential information
- **Code-First**: Prefer code snippets over lengthy descriptions
- **Impact-Driven**: Always explain business logic, UX, and UI implications
- **Actionable**: Provide clear guidance for implementation

## Code Snippet Guidelines
- Use minimal, focused examples (5-15 lines preferred)
- Show key patterns, not full implementations
- Include type signatures for clarity
- Reference existing code patterns when applicable

## Example Format

```typescript
// Before
function handleCardPlay(card: Card) {
  // existing logic
}

// After
function handleCardPlay(card: Card) {
  // new logic with change
}
```

## Draft Constraints
$ARGUMENTS
