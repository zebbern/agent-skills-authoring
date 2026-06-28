# CLAUDE.md Template

Copy and customize this template for your project. Target under 200 lines.
Remove sections that don't apply. Keep instructions specific and verifiable.

```markdown
# <Project Name> Conventions

## Commands
- Build: <build-command>
- Test: <test-command>
- Lint: <lint-command>
- Format: <format-command>
- Type check: <typecheck-command>
- Dev server: <dev-command>

## Stack
- <Language> <version> (<strict mode? type checking?>)
- <Framework> <version>
- <Runtime>
- <Database>
- <Key dependencies>

## Project Structure
- Source: <src-dir>/
- Tests: <test-dir>/ or colocated <*.test.*>
- Config: <config-dir>/
- Docs: <docs-dir>/

## Code Style
- Indentation: <N> spaces / tabs
- Naming: <camelCase/snake_case/kebab-case> for files, <PascalCase> for components
- Imports: <ES modules / CommonJS / both>, <named / default / either>
- Exports: <named / default / either>

## Rules
- <Specific rule with clear, verifiable criteria>
- <Another specific rule>
- <Example: "Run `npm test` before committing">
- <Example: "API routes return `{ data, error }` shape">

## Testing
- Runner: <vitest/jest/pytest/cargo test/go test>
- Location: <colocated / __tests__ / tests/>
- Mocking: <what to mock, what not to mock>

## Git Workflow
- Branch naming: <pattern, e.g., feat/, fix/, chore/>
- Commits: <conventional commits / free form>
- PRs: <require tests, require review, etc.>

## Additional Context
@README
@package.json
```

## Customization Guide

### Minimal CLAUDE.md (short projects)
```markdown
# Project Conventions
- Build: `npm run build`
- Test: `npm test`
- Stack: TypeScript strict, React 19
- Named exports only, tests colocated as `*.test.ts`
```

### Full CLAUDE.md (complex projects)
Expand each template section with specific, actionable rules. Include all relevant commands, conventions, and patterns. Be comprehensive — every line should help Claude work more effectively. If exceeding 200 lines:
1. Move path-specific rules to `.claude/rules/*.md`
2. Import detailed guides: `@docs/testing-guide.md`
3. Move domain knowledge to skills in `.claude/skills/`

### Multi-language projects
Use `.claude/rules/` for language-specific conventions:
```
.claude/rules/
  python-rules.md    # globs: "*.py"
  typescript-rules.md # globs: "*.ts", "*.tsx"
  rust-rules.md      # globs: "*.rs"
```
