# /update-project Checklist

Use this checklist when reviewing and updating CLAUDE.md and AGENTS.md.

## Phase 1: Read Current State

- [ ] Read `CLAUDE.md` (project root or `.claude/CLAUDE.md`)
- [ ] Read `AGENTS.md` if exists
- [ ] Read `CLAUDE.local.md` if exists
- [ ] List `.claude/` directory contents
- [ ] Read `.claude/settings.json` if exists
- [ ] Read `.claude/rules/*.md` if any
- [ ] Read `.claude/agents/*.md` if any
- [ ] Read `.claude/skills/*/SKILL.md` if any
- [ ] Read `.mcp.json` if exists

## Phase 2: Detect Actual Project State

### Build System
- [ ] Identify package manager: npm, yarn, pnpm, bun, cargo, poetry, pip, go mod, etc.
- [ ] Find build commands: `package.json` scripts, `Makefile`, `justfile`, `Cargo.toml`
- [ ] Find test runner: vitest, jest, pytest, cargo test, go test, etc.
- [ ] Find linter: eslint, pylint, clippy, golangci-lint, etc.
- [ ] Find formatter: prettier, black, rustfmt, gofmt, etc.
- [ ] Find type checker: tsc, mypy, etc.

### Framework & Stack
- [ ] Frontend framework: React, Vue, Svelte, Angular, etc.
- [ ] Backend framework: Express, FastAPI, Django, Rails, etc.
- [ ] Language version: TypeScript strict?, Python 3.12?, Go 1.24?, etc.
- [ ] Database: PostgreSQL, MongoDB, SQLite, etc.
- [ ] Key dependencies: Check package.json, requirements.txt, etc.

### Project Structure
- [ ] Source directory: `src/`, `app/`, `lib/`, `cmd/`, etc.
- [ ] Test directory structure
- [ ] Configuration files location
- [ ] Documentation: README, CONTRIBUTING, docs/

### CI/CD
- [ ] GitHub Actions, GitLab CI, CircleCI, etc.
- [ ] Deployment platform: Vercel, AWS, GCP, Fly, etc.

### Existing Conventions (from code)
- [ ] Import style: ES modules vs CommonJS, named vs default
- [ ] Naming: camelCase, PascalCase, snake_case, kebab-case
- [ ] File naming conventions
- [ ] Export patterns
- [ ] Error handling patterns

## Phase 3: Compare and Identify Gaps

### CLAUDE.md Content Check

#### Commands (must have)
- [ ] Dev server command
- [ ] Build command
- [ ] Start (production) command
- [ ] Test command (once + watch mode if available)
- [ ] Lint command
- [ ] Format command (write + check if available)
- [ ] Type check command
- [ ] Database commands (generate, migrate, seed, studio if applicable)
- [ ] Any other frequently-used scripts from package.json

#### Stack (must have)
- [ ] Language + version
- [ ] Framework + version + key features
- [ ] Database / ORM
- [ ] Key libraries (validation, styling, testing)
- [ ] Build tool / bundler

#### Code Style (should have)
- [ ] Indentation (spaces/tabs, count)
- [ ] Naming conventions (files, components, functions)
- [ ] Import/export style

#### Rules (must be specific, not vague)
- [ ] File organization (directory structure, test location)
- [ ] Component patterns (Server vs Client Components)
- [ ] API conventions (request/response shapes, error handling)
- [ ] Validation rules
- [ ] Git workflow (branch naming, commit style)
- [ ] Pre-commit checks (typecheck, test, lint)
- [ ] Security considerations

#### Testing (should have)
- [ ] Test runner
- [ ] Test file location
- [ ] Mocking policy

#### Quality
- [ ] **Under 200 lines**? If not, should rules move to `.claude/rules/`?
- [ ] **Specificity**: Every rule concrete enough to verify?
- [ ] **@imports**: Using `@README`, `@package.json` if helpful?
- [ ] **Accuracy**: All documented commands still work?
- [ ] **No contradictions** with other config files?

### AGENTS.md Check (if exists)
- [ ] Is it imported in CLAUDE.md (`@AGENTS.md`)?
- [ ] Are agent conventions still accurate?
- [ ] Any new agent-specific rules needed?

### .claude/ Directory Check
- [ ] **Subagents**: Still relevant? Tools lists accurate?
- [ ] **Skills**: Still relevant? Frontmatter valid?
- [ ] **Rules**: Globs accurate? Rules still apply?
- [ ] **Hooks**: Scripts executable? Commands still valid?
- [ ] **MCP**: Servers accessible? Config valid?

## Phase 4: Update

- [ ] Update CLAUDE.md with current commands, stack, rules
- [ ] Update AGENTS.md if needed (and ensure import works)
- [ ] Update or remove outdated subagents
- [ ] Update or remove outdated skills
- [ ] Update or remove outdated rules
- [ ] Fix broken hooks or MCP configs
- [ ] Create CLAUDE.local.md for personal prefs if needed

## Phase 5: Validate

- [ ] File parses correctly (valid markdown, valid JSON)
- [ ] All `@import` paths point to existing files
- [ ] No duplicate subagent names
- [ ] Hook scripts exist and are executable
- [ ] MCP JSON is valid JSON
