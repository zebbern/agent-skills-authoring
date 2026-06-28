# .claude Directory Structure Reference

Complete reference for the `.claude/` directory and related project files. Based on
https://code.claude.com/docs/en/claude-directory and https://code.claude.com/docs/en/memory.

## File Locations and Scopes

| File | Location | Scope | In Git? |
|------|----------|-------|---------|
| CLAUDE.md | Project root or `.claude/CLAUDE.md` | Project | Yes |
| CLAUDE.local.md | Project root | Personal | No (gitignore) |
| AGENTS.md | Project root | Shared | Yes |
| .mcp.json | Project root or `.claude/.mcp.json` | Project | Yes |
| settings.json | `.claude/settings.json` | Project | Yes |
| settings.local.json | `.claude/settings.local.json` | Personal | No (gitignore) |

## CLAUDE.md Details

**Purpose**: Project instructions Claude reads at the start of every session.

**Rules for effective CLAUDE.md**:
- Target **under 200 lines**
- Only include broadly-applicable conventions
- List build, test, lint, format commands
- Use specific instructions ("Run `npm test`" not "Test your changes")
- Use `@path/to/file` imports to pull in additional context
- Use markdown headers and bullets for structure
- Avoid contradictions with other CLAUDE.md files (check nested dirs)

**Multiple CLAUDE.md files** (monorepos):
- Nested `CLAUDE.md` files in subdirectories load in addition to root
- Use `claudeMdExcludes` in settings.json to skip irrelevant ones
- Each subagent also loads its nearest CLAUDE.md

**Import syntax**:
```markdown
See @README for project overview
# Commands
- Run tests: npm test @package.json
```
- `@file` imports at launch, max depth 4 hops
- Wrap in backticks `@file` to mention without importing

**CLAUDE.local.md**: For private preferences. Gitignored. Loaded alongside CLAUDE.md.

## AGENTS.md

- Claude Code reads `CLAUDE.md`, NOT `AGENTS.md` directly
- If AGENTS.md exists (for other agents), import it:
  ```markdown
  @AGENTS.md
  ## Claude Code Specific
  Use plan mode for changes under `src/billing/`.
  ```
- Only create AGENTS.md if multiple tools need shared instructions

## Subagents (.claude/agents/)

**Format**: Markdown files with YAML frontmatter.

```markdown
---
name: code-reviewer
description: Reviews code for quality and patterns
tools: Read, Grep, Glob, Bash
model: sonnet
---
You are a senior engineer. Review code for:
- Correctness and edge cases
- Consistency with project patterns
- Test coverage
Provide specific line references.
```

**Frontmatter fields**:
- `name` (required): Unique identifier
- `description` (required): When to delegate to this agent
- `tools`: Comma-separated allowed tools (default: all)
- `model`: Model override (`sonnet`, `opus`, `haiku`)
- `scope`: `project` or `user` (determines memory location)

**Scope priority** (highest to lowest):
1. Managed settings (org-wide)
2. `--agents` CLI flag (current session)
3. `.claude/agents/` (current project)
4. `~/.claude/agents/` (all projects)
5. Plugin's `agents/` directory

**Naming**: Keep `name` unique across the tree. Duplicates within one scope = one discarded silently.

## Skills (.claude/skills/)

**Format**: `<skill-name>/SKILL.md` with YAML frontmatter.

```markdown
---
name: api-conventions
description: REST API design conventions for our services
---
# API Conventions
- Use kebab-case for URL paths
- Use camelCase for JSON properties
```

**Fields**:
- `name`: Unique skill identifier
- `description`: When to apply this skill (triggers auto-loading)
- `disable-model-invocation: true`: For manual-only skills

**Invocation**:
- Auto-loaded when description matches the task
- Manual: `/skill-name` or `/skill-name arguments`

## Rules (.claude/rules/)

Path-scoped instructions that load only when working with matching files.

```markdown
---
globs: "*.test.ts", "*.spec.ts"
---
# Test Conventions
- Use vitest, not jest
- Colocate tests: `foo.ts` -> `foo.test.ts`
- Mock external APIs, not internal modules
```

**Fields**:
- `globs`: Comma-separated file patterns (required)

## Hooks (.claude/hooks/ or settings.json hooks)

Shell commands, HTTP endpoints, or LLM prompts that execute at lifecycle points.

**Configuration in settings.json**:
```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "tool:Write",
        "command": ".claude/hooks/format.sh"
      }
    ]
  }
}
```

**Events**: SessionStart, SessionEnd, UserPromptSubmit, Stop, StopFailure, PreToolUse, PostToolUse, Setup, InstructionsLoaded

## MCP (.mcp.json)

Model Context Protocol server configuration.

```json
{
  "mcpServers": {
    "postgres": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres", "postgresql://..."]
    }
  }
}
```

## Settings (.claude/settings.json)

```json
{
  "permissions": {
    "allow": ["Bash:npm test", "Bash:npm run build"],
    "deny": ["Bash:rm -rf /"]
  },
  "hooks": { ... },
  "env": { "NODE_ENV": "development" }
}
```

## Memory (Auto Memory)

- Claude accumulates learnings automatically based on corrections
- Stored in `.claude/agent-memory/` (per-repo, not committed)
- First 200 lines or 25KB loaded per session
- Edit via `/memory` command

## Skills CLI Quick Reference

The `npx skills` CLI manages skills from skills.sh. Key commands:

```bash
npx skills find [keyword]     # Search skills (interactive if no keyword)
npx skills add <owner/repo>   # Install a skill
npx skills list               # List installed skills
npx skills ls -g              # List global skills only
npx skills check              # Check for updates
npx skills update             # Update all skills
npx skills remove <name>      # Remove a skill
npx skills init <name>        # Create skill scaffold
npx skills generate-lock      # Generate skills-lock.json
```

**Install scope**:
- Project (default): installs to `.claude/skills/` — commit to git, shared with team
- Global (`-g`): installs to `~/.claude/skills/` — available in all projects
