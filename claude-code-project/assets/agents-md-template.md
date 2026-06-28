# AGENTS.md Template

AGENTS.md is for sharing instructions across multiple AI agents (Claude Code,
GitHub Copilot, etc.). Claude Code reads CLAUDE.md, not AGENTS.md directly —
import AGENTS.md from CLAUDE.md instead.

## When to Use

Create AGENTS.md ONLY if:
- Multiple AI tools work on this repository
- You want shared conventions across all agents
- Other tools (not just Claude Code) read AGENTS.md

If only Claude Code uses this repo, put everything in CLAUDE.md and skip AGENTS.md.

## Template

```markdown
# Agent Instructions for <Project Name>

## Overview
<Project description and purpose>

## Technology Stack
- Language: <language> <version>
- Framework: <framework> <version>
- Runtime: <runtime>

## Code Conventions
- <Shared conventions all agents should follow>
- <File organization rules>
- <Naming conventions>
- <Import/export patterns>

## Architecture
- <High-level architecture description>
- <Key directories and their purposes>
- <Data flow or API patterns>

## Testing
- <Test conventions>
- <Coverage expectations>

## Security
- <Security-sensitive patterns>
- <What NOT to do (e.g., don't log secrets)>

## Common Tasks
- <How to add a new feature>
- <How to add a new API endpoint>
- <How to run migrations>
```

## Integration with Claude Code

### In CLAUDE.md:
```markdown
@AGENTS.md

## Claude Code Specific
- Use plan mode for changes touching >3 files
- Run `/test` after implementing features
- Prefer subagents for research and exploration tasks
```

Claude loads AGENTS.md first, then appends the Claude-specific instructions.
Keep Claude-specific instructions in CLAUDE.md, not in AGENTS.md.
