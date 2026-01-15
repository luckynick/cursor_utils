# Generate Commit Message

Generate a concise, well-structured commit message for staged changes.

## Process

1. Run `git diff --cached` to see staged changes
2. Run `git diff --cached --stat` to see file summary
3. Analyze the changes and generate a commit message

## Commit Message Format

```
<type>: <subject>

<body>
```

### Type (lowercase)
- `feat` - New feature
- `fix` - Bug fix
- `refactor` - Code restructuring without behavior change
- `style` - Formatting, styling changes
- `test` - Adding or updating tests
- `docs` - Documentation only
- `chore` - Build, config, tooling changes

### Subject Line
- Imperative mood ("Add feature" not "Added feature")
- No period at the end
- Max 50 characters
- Lowercase after type

### Body
- Bullet points with `-` prefix
- Group related changes
- Focus on WHAT changed and WHY, not HOW
- Keep each bullet concise (1 line ideally)

## Example Output

```
feat: add tutorial system with guided gameplay

- Tutorial store and step configuration
- Scripted card dealing for deterministic scenarios
- Arrow component for guided card selection
- Welcome, tiebreaker, combo, and end-game explanations
```

## Rules

- If changes span multiple unrelated areas, suggest splitting into separate commits
- Mention affected feature areas (e.g., "audio system", "game board", "settings")
- Don't list every file - summarize by feature/purpose
- If there are test files, mention "with tests" rather than listing test files

## Task

$ARGUMENTS
