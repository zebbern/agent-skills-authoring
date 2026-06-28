# /verify-project Checklist

Comprehensive verification of the entire Claude Code project setup.

## 1. File Structure Verification

### Required Files (at least one of)
- [ ] `CLAUDE.md` exists at project root OR `.claude/CLAUDE.md` exists

### Optional but Common Files
- [ ] `AGENTS.md` exists (only if other agents use it too)
- [ ] `.claude/settings.json` exists and is valid JSON
- [ ] `.claude/settings.local.json` exists (personal, gitignored)
- [ ] `.mcp.json` exists and is valid JSON
- [ ] `CLAUDE.local.md` exists (personal, gitignored)

### Directory Structure
- [ ] `.claude/` directory exists (if using nested config)
- [ ] `.claude/skills/` exists and each skill has its own subdirectory
- [ ] `.claude/agents/` exists and agent files are `.md`
- [ ] `.claude/rules/` exists and rule files are `.md` with `globs` frontmatter
- [ ] `.claude/hooks/` exists and hook scripts are executable

## 2. CLAUDE.md Validation

### Content Quality
- [ ] Under 200 lines (warn if over)
- [ ] Has Commands section with build/test/lint
- [ ] Has Stack or Technology section
- [ ] Has specific, actionable rules (not vague)
- [ ] Uses markdown headers for organization
- [ ] No contradictions with other config files

### Import Validation
- [ ] All `@path/to/file` references point to existing files
- [ ] No import loops (max depth is 4)
- [ ] Backtick-wrapped `@file` mentions are intentional (not accidental imports)

### Location
- [ ] Only one primary CLAUDE.md (root OR .claude/, not both with conflicts)
- [ ] If monorepo, nested CLAUDE.md files don't contradict parent

## 3. AGENTS.md Validation

- [ ] File is valid markdown
- [ ] If exists, CLAUDE.md imports it (`@AGENTS.md`)
- [ ] CLAUDE.md has additional Claude-specific instructions after import
- [ ] No Claude Code-specific instructions in AGENTS.md (those belong in CLAUDE.md)

## 4. Subagents Validation (.claude/agents/*.md)

### Per-Agent Checks
- [ ] File has YAML frontmatter with `---` delimiters
- [ ] `name` field exists and is unique across all scopes
- [ ] `description` field exists and is specific
- [ ] `tools` field lists valid tool names (Read, Grep, Glob, Bash, Write, Edit, etc.)
- [ ] `model` field is valid if specified (sonnet, opus, haiku)
- [ ] Body content provides clear instructions

### Naming Conflicts
- [ ] No duplicate `name` values within `.claude/agents/`
- [ ] No duplicate `name` values within `~/.claude/agents/`
- [ ] No conflict with built-in subagent names (Explore, Plan, etc.)

## 5. Skills Validation (.claude/skills/*/SKILL.md)

### Per-Skill Checks
- [ ] Directory structure: `.claude/skills/<name>/SKILL.md`
- [ ] YAML frontmatter has `name` matching directory name
- [ ] `description` field exists (triggers auto-loading)
- [ ] `disable-model-invocation` used correctly if manual-only
- [ ] Body has actionable instructions
- [ ] References to `references/` or `scripts/` files resolve

## 6. Rules Validation (.claude/rules/*.md)

- [ ] Each file has YAML frontmatter with `globs` field
- [ ] `globs` is comma-separated list of valid glob patterns
- [ ] Body has specific instructions for matched files
- [ ] Rules don't contradict CLAUDE.md

## 7. Hooks Validation

### settings.json Hooks
- [ ] `hooks` key in settings.json is valid object
- [ ] Each event (SessionStart, PreToolUse, etc.) is a valid event name
- [ ] Each hook has `matcher` and `command` or `url`
- [ ] `matcher` patterns are valid
- [ ] `command` references exist in `.claude/hooks/` or are in PATH

### Hook Scripts
- [ ] Scripts in `.claude/hooks/` are executable (`chmod +x`)
- [ ] Scripts reference commands available in PATH
- [ ] Scripts handle stdin JSON input correctly
- [ ] Scripts return valid exit codes (0=allow, 1=deny, 2=special per event)

## 8. MCP Validation (.mcp.json)

- [ ] Valid JSON syntax
- [ ] `mcpServers` object exists
- [ ] Each server has `command` or `url`
- [ ] `command` is in PATH or uses `npx -y` pattern
- [ ] `args` array is valid
- [ ] Environment variables reference existing vars or are hardcoded

## 9. Settings Validation

- [ ] `.claude/settings.json` is valid JSON
- [ ] `permissions.allow` and `permissions.deny` entries are valid patterns
- [ ] No conflicting allow/deny rules
- [ ] `env` variables don't expose secrets
- [ ] `.claude/settings.local.json` is gitignored (check `.gitignore`)

## 10. Cross-Reference Validation

- [ ] CLAUDE.md @imports reference existing files
- [ ] Subagent `tools` lists contain valid tool names
- [ ] Skill references to `scripts/` or `references/` resolve
- [ ] Hook commands reference existing scripts
- [ ] MCP server commands are available

## 11. Git Hygiene

- [ ] CLAUDE.md is committed to git (should be shared)
- [ ] CLAUDE.local.md is in .gitignore (personal)
- [ ] settings.local.json is in .gitignore (personal)
- [ ] `.claude/agent-memory/` is in .gitignore (auto-generated)
- [ ] No secrets in committed files

## 12. Functional Tests (where possible)

- [ ] Build command from CLAUDE.md succeeds: `npm run build` etc.
- [ ] Test command succeeds: `npm test` etc.
- [ ] Lint command succeeds: `npm run lint` etc.
- [ ] MCP servers respond (if tools available to test)

## Verification Report Template

```markdownn# Verification Report: <project-name>

| Category | Status | Issues |
|----------|--------|--------|
| File Structure | ✅/⚠️/❌ | <count> |
| CLAUDE.md | ✅/⚠️/❌ | <count> |
| AGENTS.md | ✅/⚠️/❌ | <count> |
| Subagents | ✅/⚠️/❌ | <count> |
| Skills | ✅/⚠️/❌ | <count> |
| Rules | ✅/⚠️/❌ | <count> |
| Hooks | ✅/⚠️/❌ | <count> |
| MCP | ✅/⚠️/❌ | <count> |
| Settings | ✅/⚠️/❌ | <count> |
| Cross-References | ✅/⚠️/❌ | <count> |
| Git Hygiene | ✅/⚠️/❌ | <count> |

## Critical Issues (must fix)
1. ...

## Warnings (should fix)
1. ...

## Passed Checks
- ...
```
