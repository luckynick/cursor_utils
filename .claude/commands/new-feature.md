# New Feature Implementation

## Test-Driven Validation
Write tests before concluding changes to ensure:
- Tests validate actual behavior, not just that code compiles
- Regressions are caught before merging
- E2E tests verify user-facing flows end-to-end

## Code Conventions

**TypeScript**: Use `interface` over `type` for objects; import types from `src/types/`; avoid `any`.

**React**: Named exports; hooks at top; `useCallback` for prop handlers; prefix handlers with `handle`.

**Styling**: Tailwind for layout/spacing/colors; SCSS for complex animations; use `clsx` for conditionals.

**Component Reuse**: Check `src/components/ui/` and `src/components/game/` first. Prefer composing existing components (`CasinoButton`, `MatchRibbon`, `BaseModal`).

**Modals**: `BaseModal` for low-level overlay + focus trap. `ModalContainer` for standard background. Use `BaseModal` directly for transparent overlays.

**i18n**: `en.yml` is source of truth. Use fully qualified keys: `t('gameEnd.playAgain')` not `t('playAgain')`. Preserve existing quote style when editing.

**Assets**: Images in `public/images/` organized by feature.

## Validation
- Run `npm test` after substantial changes
- Run E2E tests after changes affecting user flows
- Never disable lint/type errors if viable alternative exists

## Feature Description
$ARGUMENTS
