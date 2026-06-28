# /check Improvements Guide

Comprehensive guide for identifying configuration improvements in Claude Code projects.

## Category 1: CLAUDE.md Quality

### Length
- **Ideal**: Under 200 lines
- **Over 200**: Suggest moving path-specific rules to `.claude/rules/`
- **Over 300**: Strongly recommend restructuring with rules + imports

### Content Coverage Check
CLAUDE.md should typically include:
- [ ] **Commands**: Build, test, lint, format (specific command strings)
- [ ] **Stack**: Language, framework, runtime version
- [ ] **Style**: Indentation, naming, import style
- [ ] **Structure**: Directory conventions, file placement
- [ ] **Testing**: Test runner, test location, mocking policy
- [ ] **API**: Response shapes, error handling
- [ ] **Git**: Branch naming, commit style, PR process

### Specificity Score
For each rule, check if it's verifiable:
- ✅ "Use 2-space indentation" — specific
- ✅ "Run `npm test` before committing" — specific
- ⚠️ "Format code properly" — vague, needs specificity
- ⚠️ "Keep files organized" — vague, needs directory rules

### Import Usage
- [ ] Could `@README` add useful context?
- [ ] Could `@package.json` expose available commands?
- [ ] Could `@docs/*.md` import detailed guides?
- [ ] Are imports used instead of duplicating content?

## Category 2: Missing Tool Documentation

Check if these common tools are documented in CLAUDE.md:

### JavaScript/TypeScript
- Build: `vite build`, `tsc`, `next build`, `rollup -c`
- Test: `vitest`, `jest`, `playwright test`, `cypress run`
- Lint: `eslint`, `biome lint`, `oxlint`
- Format: `prettier`, `biome format`
- Type check: `tsc --noEmit`
- Package manager: `npm`, `yarn`, `pnpm`, `bun`

### Python
- Build: `pip install -e .`, `poetry build`, `uv build`
- Test: `pytest`, `unittest`, `tox`
- Lint: `ruff check`, `pylint`, `flake8`
- Format: `ruff format`, `black`
- Type check: `mypy`, `pyright`

### Rust
- Build: `cargo build`, `cargo build --release`
- Test: `cargo test`, `cargo nextest run`
- Lint: `cargo clippy`, `cargo clippy -- -D warnings`
- Format: `cargo fmt`

### Go
- Build: `go build`, `go build ./...`
- Test: `go test ./...`, `go test -race ./...`
- Lint: `golangci-lint run`
- Format: `gofmt`, `goimports`

### Docker
- Build: `docker build -t <image> .`, `docker compose build`
- Run: `docker compose up`, `docker run -p 3000:3000 <image>`

## Category 3: MCP Opportunities

Look for these external services that benefit from MCP:

### Databases
- PostgreSQL → `@modelcontextprotocol/server-postgres`
- SQLite → `@modelcontextprotocol/server-sqlite`
- MySQL → `@modelcontextprotocol/server-mysql`
- MongoDB → Custom MCP or community server

### Cloud Services
- AWS → Community MCP servers for S3, Lambda, etc.
- GitHub → `@modelcontextprotocol/server-github`
- Slack → Community MCP server
- Linear → Community MCP server

### Development Tools
- Browser automation → Playwright MCP
- Documentation → Figma MCP, Notion MCP
- Monitoring → Datadog, Sentry MCP servers

### How to Add MCP
1. Install server: `claude mcp add <name> <command>`
2. Or edit `.mcp.json` directly:
   ```json
   {
     "mcpServers": {
       "mydb": {
         "command": "npx",
         "args": ["-y", "@modelcontextprotocol/server-postgres", "<connection-string>"]
       }
     }
   }
   ```

## Category 4: Skill Gaps (use `npx skills` CLI)

Use the Skills CLI to discover and evaluate skills. Do NOT browse the website — use terminal commands.

### Discovery Workflow

1. **Check what's installed**: `npx skills list`
2. **Search by keyword**: `npx skills find <keyword>`
   - `npx skills find react` — React/Next.js skills
   - `npx skills find testing` — Testing/TDD skills
   - `npx skills find design` — UI/UX design skills
   - `npx skills find review` — Code review skills
3. **Interactive search**: `npx skills find` (opens fzf-style picker)
4. **Verify quality before recommending**:
   - Prefer 1K+ installs
   - Prefer official sources: `anthropics/*`, `vercel-labs/*`, `microsoft/*`
   - Check GitHub stars of the source repo

### Common Skills to Suggest (search with `npx skills find <keyword>`)

| Topic | Search Command | Typical Results |
|-------|---------------|----------------|
| React/Next.js | `npx skills find react` | vercel-labs React best practices |
| Testing | `npx skills find testing` | TDD, testing patterns |
| Design | `npx skills find design` | Frontend design guidelines |
| Code review | `npx skills find review` | PR review skills |
| Architecture | `npx skills find architecture` | Refactoring, patterns |
| Security | `npx skills find security` | Security guidance |

### Install Command
```bash
# Install a discovered skill
npx skills add <owner/repo>

# Install to project (committed, team-shared)
npx skills add <owner/repo>

# Install globally (all projects)
npx skills add <owner/repo> -g

# Non-interactive (CI/CD friendly)
npx skills add <owner/repo> -y
```

### When to Create Custom Skills
Consider creating a `.claude/skills/` skill when:
- Repetitive workflow (e.g., "fix issue → test → lint → commit → PR")
- Domain knowledge (e.g., "our API conventions")
- Complex multi-step process

Create scaffold: `npx skills init <skill-name>`

## Category 5: Subagent Opportunities

Consider creating subagents when:
- [ ] Security reviews happen regularly → `security-reviewer`
- [ ] Code quality audits happen → `code-reviewer`
- [ ] Research tasks flood context → `researcher`
- [ ] Testing needs specialized focus → `test-writer`
- [ ] Documentation needs updates → `docs-writer`
- [ ] Migration/refactoring tasks → `migration-agent`

### Subagent Template
```markdown
---
name: <agent-name>
description: <when to delegate>
tools: Read, Grep, Glob, Bash[, Write, Edit]
model: sonnet
---
<Clear instructions for the agent>
```

## Category 6: Hook Opportunities

Common useful hooks to suggest:

### PreToolUse (before tool execution)
- **Format on write**: Auto-format code after Write/Edit tool
- **Lint check**: Run linter before allowing edits
- **Test guard**: Prompt before running destructive commands

### SessionStart (once per session)
- **Environment setup**: Verify env vars, check dependencies
- **Git status**: Show current branch and uncommitted changes

### Stop (when task completes)
- **Git reminder**: Suggest commit if changes exist
- **Format check**: Run formatter on all modified files

### UserPromptSubmit (before processing prompt)
- **Spell check**: Quick typo detection
- **Command validation**: Validate referenced commands exist

## Category 7: Rules Organization

### When to Use Path-Scoped Rules
- [ ] Monorepo with different conventions per package
- [ ] Frontend and backend with different styles
- [ ] Test files with different conventions than source
- [ ] Legacy code with different patterns

### Example Rules
```markdown
---
globs: "*.test.ts", "*.spec.ts"
---
# Test Rules
- Use vitest (not jest)
- Colocate: `foo.ts` → `foo.test.ts`
- Mock external APIs only
```

```markdown
---
globs: "src/server/**/*.ts"
---
# Backend Rules
- Use Fastify decorators for routes
- Return `{ success, data?, error? }` from handlers
- Validate with Zod schemas
```

## Category 8: Settings Optimization

### Permission Modes
- `acceptEdits`: Ask before file edits (safest for new projects)
- `acceptAll`: Auto-accept file edits (trusted projects)
- `auto`: Full autonomy (well-tested projects with hooks)

### Model Selection
- Default `sonnet` for most work
- `opus` for complex architectural changes
- `haiku` for fast subagent tasks (explore, grep)

### Context Management
- [ ] Custom status line showing context usage
- [ ] `/compact` strategy for long sessions
- [ ] Subagents for heavy research tasks

## Quick Wins Checklist

These are the highest-impact, lowest-effort improvements:

- [ ] Add build/test/lint commands to CLAUDE.md if missing
- [ ] Ensure CLAUDE.md is under 200 lines
- [ ] Import `@README` and `@package.json` in CLAUDE.md
- [ ] Create CLAUDE.local.md for personal prefs (gitignore it)
- [ ] Ensure AGENTS.md is imported in CLAUDE.md (if both exist)
- [ ] Add `.claude/agent-memory/` to .gitignore
- [ ] Verify no secrets in committed config files
- [ ] Run `/init` if CLAUDE.md doesn't exist yet
