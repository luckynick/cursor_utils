# Align UI with Figma Design

## Command Purpose
Iteratively refine UI to achieve 99% visual match with Figma design screenshot using computer vision to perceive design details, browser tools for visual verification, and automated testing.

## Core Principles

1. **Always Reference Design First**: **MANDATORY** - Use computer vision to visually perceive and analyze the Figma design screenshot before approaching each refinement item. Use `read_file` on the design image file to view and analyze visual details (layout, spacing, colors, typography, borders, shadows, etc.)
2. **Iterative Refinement**: Incremental changes → visual verification → repeat until 99% match
3. **Visual Verification First**: Always use browser tools to inspect changes, not just code review
4. **Screenshot Comparison**: Capture screenshots after each change to compare with design
5. **Test After Changes**: Run automated tests at the end of each refinement round

## Process

### Phase 1: Setup
1. **Load and Perceive Design**: Use `read_file` on the Figma design screenshot to visually analyze it with computer vision - examine layout, spacing, colors, typography, borders, shadows, alignment, and all visual details
2. Navigate to target UI state using `browser_navigate`, `browser_click`, `browser_snapshot`
3. Capture initial state with `browser_take_screenshot`
4. Compare current implementation with perceived design details and create TODO list of discrepancies

### Phase 2: Iterative Refinement Loop
Repeat until 99% match:

1. **Check Design**: **REQUIRED** - Use `read_file` to visually perceive and analyze the Figma design screenshot with computer vision before approaching each refinement item. Examine the specific visual details relevant to the current TODO item
2. **Identify**: Compare current screenshot with visually perceived design details, prioritize discrepancies
3. **Change**: Make targeted code changes (one category per iteration: spacing → colors → typography)
4. **Verify**: `browser_wait_for({ time: 2 })` → `browser_take_screenshot` → compare with design
5. **Iterate**: If correct, move to next item (repeat from step 1); if not, adjust and verify again

### Phase 3: Final Verification
1. **Visual Check**: Final screenshot comparison - verify layout, spacing, colors, typography, borders, shadows, alignment, interactive states
2. **Run Tests**: `npm test`, `npm run test:e2e`, `npm run lint`, TypeScript compilation
3. **Responsive** (if applicable): Verify breakpoints match design variants

## Browser Tools Usage

**Navigation:**
```typescript
browser_navigate({ url: "http://localhost:3000" })
browser_snapshot() // Understand page structure
browser_click({ element: "...", ref: "..." })
browser_wait_for({ time: 2 }) // Wait for animations/loading
```

**Visual Verification:**
```typescript
browser_take_screenshot({ filename: "iteration-1.png", fullPage: false })
browser_take_screenshot({ element: "Specific area", filename: "detail.png" })
```

**Interaction:** `browser_click`, `browser_type`, `browser_select_option`, `browser_wait_for`

## Common Discrepancies Checklist

- **Layout & Spacing**: Padding, margins, gaps, widths/heights, alignment
- **Visual Styling**: Colors (exact hex), border (width/style/color), border-radius, box-shadow, gradients, opacity
- **Typography**: Font family, size, weight, line-height, letter-spacing, text alignment/color
- **Components**: Button styles, inputs, modals, scrollbars, icons, images

## Code Modification Guidelines

1. **Locate**: Use `codebase_search` and `grep` to find relevant components/styles
2. **Change**: Minimal, targeted changes only - avoid over-engineering
3. **CSS Specificity**: Check for conflicts, avoid `!important`, remove redundant styles, verify third-party component props (e.g., `noDefaultStyles`)
4. **Responsive**: Apply styles in appropriate media queries if design includes breakpoints

## Success Criteria

- ✅ 99% visual match (screenshot comparison)
- ✅ All tests pass (`npm test`, `npm run test:e2e`)
- ✅ No linting/TypeScript errors
- ✅ No functionality regressions

## Quick Reference Workflow

```
1. Perceive design: read_file → design screenshot (use computer vision to analyze visual details)
2. browser_navigate → app URL
3. Navigate to target state (clicks, typing)
4. browser_take_screenshot → "initial.png"
5. Compare current state with perceived design details → create TODO list
6. FOR EACH TODO ITEM:
   a. read_file → design screenshot (REQUIRED - use computer vision to perceive relevant visual details)
   b. Compare current state with visually perceived design
   c. Make targeted change
   d. wait 2s → screenshot → compare with design
   e. Repeat until item matches
7. npm test && npm run test:e2e
8. Final screenshot comparison with visually perceived design
```

## Key Reminders

- **MANDATORY**: Use computer vision to visually perceive and analyze design screenshots with `read_file` before each refinement item
- Actually view and analyze image content (layout, spacing, colors, borders, shadows, typography) - don't just read file metadata
- Verify in browser, not just code inspection
- Small, focused changes per iteration
- Screenshot after each change for comparison
- Run tests after significant changes
- Track progress with TODOs
