---
name: agent-skills-authoring
description: >-
  Instructs AI agents how to create, validate, and optimize Agent Skills (SKILL.md files)
  from user requests. Covers all YAML frontmatter fields, body structure, progressive
  disclosure, and quality patterns. Use when a user asks you to create, edit, or improve
  an Agent Skill.
compatibility: >-
  Any AI agent generating SKILL.md files. Follows the Agent Skills specification.
metadata:
  spec-version: "1.1"
  source: "https://github.com/zebbern/agent-skills-guide"
  audience: "ai-agents"
allowed-tools: skills-ref
---

# Agent Skills Authoring Guide

You are reading this because a user wants you to create, edit, or improve an Agent Skill.
This is your authoritative reference for producing high-quality SKILL.md files.

**Your goal:** Transform the user's request into the best possible SKILL.md — one that
any consuming agent can parse, understand, and apply effectively.

---

## 1. What Agent Skills Are

An Agent Skill is a portable, model-agnostic instruction package stored as a directory.
It tells an AI agent **what to do**, **when to activate**, and **how to behave** for a
specific domain. Skills are consumed at inference time — NOT code libraries, NOT prompt
templates, NOT fine-tuning datasets.

- Plain text Markdown, no compilation
- One skill per directory, one `SKILL.md` per skill
- Portable across agents, platforms, and models
- Discoverable via description keywords
- Validated via `skills-ref validate`

---

## 2. Directory Structure

```
skill-name/
├── SKILL.md          # REQUIRED — frontmatter + instructions
├── examples/         # Optional — reference files
│   └── sample.json
└── templates/        # Optional — starter files
    └── component.tsx
```

- Directory name MUST match the `name` field in frontmatter exactly
- `SKILL.md` is the only required file
- Bundled files use `{{file:path}}` syntax, resolve ONE level deep only

---

## 3. YAML Frontmatter — Complete Field Reference

The spec defines exactly **six fields**. No others exist. Use `---` delimiters.

### `name` (REQUIRED)

Unique identifier. 1–64 chars. Unicode lowercase alphanumeric + hyphens only.
No leading/trailing/consecutive hyphens. Must match parent directory name.

```yaml
name: api-design-patterns
```

**Rules:** Derive from primary function. Use hyphens between words. Be specific
(`react-component-gen` not `component-gen`) and concise (`git-workflow` not
`git-version-control-workflow-management`).

### `description` (REQUIRED)

Natural-language summary for discoverability. 1–1,024 chars. Plain text only.
Agents match user requests against this to decide which skills to load.

```yaml
description: >-
  Guides agents through REST API endpoint design including naming conventions,
  HTTP methods, status codes, pagination, and error responses. Use when designing
  API endpoints, reviewing APIs, or generating OpenAPI specs.
```

**Rules:** Start with action verb. Include 3-5 keyword triggers. State "Use when..."
Front-load important info. Aim for 150-300 chars.

### `license` (OPTIONAL)

SPDX identifier or `{{file:LICENSE}}` reference. Omit if unknown.

```yaml
license: MIT
```

### `compatibility` (OPTIONAL)

Environment requirements. 1–500 chars. Plain text.

```yaml
compatibility: >-
  VS Code with GitHub Copilot. Requires Node.js 18+ and npm.
```

### `metadata` (OPTIONAL)

Arbitrary key-value map. Keys and values MUST be strings. Use unique key names.

```yaml
metadata:
  author: "your-org"
  category: "code-quality"
  spec-version: "1.0"
  last-updated: "2026-03-01"
```

### `allowed-tools` (OPTIONAL, EXPERIMENTAL)

Space-delimited pre-approved tools list. Not all platforms honor this.

```yaml
allowed-tools: file-read file-write terminal-run web-fetch
```

---

## 4. Body Content Structure

Everything after the closing `---` is free-form Markdown. This structure produces
the most effective skills:

```markdown
# [Skill Name]

[1-3 sentence overview]

## When to Activate

[Trigger conditions for loading this skill]

## Core Rules

[Prioritized rules with [P0-MUST] / [P1-SHOULD] / [P2-MAY] markers]

## Patterns

[Common patterns with code examples]

## Anti-Patterns

[What NOT to do]

## Checklist

[Validation checklist for the consuming agent]
```

### Body Writing Rules

**DO:** Use imperative voice ("Use X", "Never do Y"). Use priority markers.
Use code blocks with language tags. Use tables for comparisons. Use headers for
scannable sections. Keep Tier 2 body under 5,000 tokens. Front-load critical rules.

**DO NOT:** Use conversational tone. Include HTML. Include images. Write more than
needed — brevity improves agent compliance.

### Progressive Disclosure (Token Budget)

| Tier | Content                          | Budget         | Purpose                     |
| ---- | -------------------------------- | -------------- | --------------------------- |
| 1    | Frontmatter (name + description) | ~100 tokens    | Quick relevance match       |
| 2    | Core rules + key patterns        | < 5,000 tokens | Full execution instructions |
| 3    | File references, templates       | As needed      | Deep-dive loaded on demand  |

Keep Tier 2 lean. Put verbose examples in bundled files referenced via `{{file:path}}`.

### File References

```markdown
See the template: {{file:templates/component.tsx}}
```

- Paths relative to skill directory root
- Resolve ONE level deep (no recursive nesting)
- Use for long examples and templates, NOT for core rules

---

## 5. Creating a Skill from a User Request

### Step 1: Interpret the Request

- **Domain:** What subject? (testing, API design, accessibility, etc.)
- **Scope:** Narrow (one pattern) or broad (entire workflow)?
- **Constraints:** Platform, language, tool dependencies?
- Ask clarifying questions if ambiguous

### Step 2: Choose the Name

- Format: `{domain}-{action}` or `{domain}-{qualifier}`
- Validate: lowercase, hyphens, 1-64 chars, no leading/trailing/consecutive hyphens

### Step 3: Write the Description

- Start with action verb. Include keyword triggers. State "Use when..."
- Aim for 150-300 characters. Stay under 1,024.

### Step 4: Set Optional Fields

- **license:** User-specified or `MIT` or omit
- **compatibility:** Set if skill requires specific tools/runtimes/platforms
- **metadata:** Always add `category` and `spec-version`. Add `last-updated` with today's date
- **allowed-tools:** List tools the skill's instructions will ask agents to use

### Step 5: Write the Body

- 1-3 sentence overview → "When to Activate" → "Core Rules" with priority markers →
  Patterns with code → Anti-patterns → Validation checklist

### Step 6: Validate

- Directory name === `name` field
- Description: 1-1,024 chars, action verb, keyword triggers
- File references resolve to existing files
- Tier 2 under ~5,000 tokens
- Run `skills-ref validate` if available

---

## 6. Quality Patterns

### Prioritized Rules

```markdown
- [P0-MUST] Validate all inputs at system boundaries.
- [P1-SHOULD] Use structured logging with correlation IDs.
- [P2-MAY] Add request/response timing headers.
```

### Decision Tables

```markdown
| Scenario      | Approach          | Reason               |
| ------------- | ----------------- | -------------------- |
| < 100 items   | Inline array      | No pagination needed |
| 100-10K items | Cursor pagination | Stable ordering      |
| > 10K items   | Keyset pagination | O(1) fetch           |
```

### Before/After Examples

```markdown
### Bad

app.get('/getUser', ...)

### Good

app.get('/users/:id', ...)
```

### Conditional Instructions

```markdown
### If TypeScript:

- [P0-MUST] Use strict mode

### If JavaScript:

- [P1-SHOULD] Use JSDoc annotations
```

### Workflow Sequences

```markdown
1. **Analyze** — Read codebase for patterns
2. **Plan** — Identify changes and dependencies
3. **Implement** — Follow Core Rules
4. **Validate** — Run Checklist
```

---

## 7. Anti-Patterns

| Anti-Pattern        | Problem                          | Fix                                          |
| ------------------- | -------------------------------- | -------------------------------------------- |
| Vague description   | Agents can't match to requests   | Add keyword triggers + "Use when..."         |
| Wall of text        | Agents lose focus, truncate      | Headers, lists, tables. Tier 2 < 5K tokens   |
| Conversational tone | Agents process directives better | Imperative: "Do X" not "You might want to X" |
| No priority markers | Agent treats all rules equally   | Use `[P0-MUST]` / `[P1-SHOULD]` / `[P2-MAY]` |
| Code in description | Violates spec, breaks parsers    | Code in body only. Description is plain text |
| HTML in body        | Not universally supported        | Pure Markdown only                           |
| No checklist        | No self-validation step          | Always end with quick-check list             |
| Name mismatch       | Validation fails instantly       | Directory name === `name` field              |
| Images              | Agents cannot process them       | Text diagrams or code blocks                 |

---

## 8. Security

- [P0-MUST] NEVER include secrets, API keys, or credentials in SKILL.md or bundled files
- [P0-MUST] NEVER instruct agents to disable security controls
- [P1-SHOULD] Include input validation rules for skills handling user data
- [P1-SHOULD] Include sanitization rules for skills generating browser-rendered output

---

## 9. Testing

1. **Syntax:** `skills-ref validate` — confirms structure and name match
2. **Activation:** Prompt an agent with a request that SHOULD trigger the skill
3. **Negative:** Prompt with something that should NOT trigger it
4. **Quality:** Have the agent execute — verify output follows the skill's rules
5. **Edge cases:** Try ambiguous prompts to test boundary behavior

---

## 10. Resources

| Resource                                   | URL                                  |
| ------------------------------------------ | ------------------------------------ |
| Agent Skills Specification                 | https://agentskills.io/specification |
| Anthropic Skills Repository (79.5K+ stars) | https://github.com/anthropics/skills |
| Agent Skills Registry                      | https://agentskills.io/registry      |
| skills-ref CLI                             | https://agentskills.io/tools         |
| Skill Directory Template                   | https://agentskills.io/template      |

---

## 11. Pre-Delivery Checklist

### Frontmatter

- [ ] `name`: present, 1-64 chars, lowercase alphanum + hyphens, matches directory
- [ ] `description`: present, 1-1,024 chars, action verb, keyword triggers, "Use when..."
- [ ] Optional fields set where relevant — no invented fields
- [ ] No non-spec fields present

### Body

- [ ] 1-3 sentence overview
- [ ] Trigger section ("When to Activate" or equivalent)
- [ ] Rules use `[P0-MUST]` / `[P1-SHOULD]` / `[P2-MAY]` markers
- [ ] Code examples with language-tagged fenced blocks
- [ ] Anti-patterns or warnings section
- [ ] Validation checklist for consuming agent
- [ ] Tier 2 under ~5,000 tokens
- [ ] Pure Markdown — no HTML, no images

### Files

- [ ] All `{{file:path}}` references resolve to real files
- [ ] No secrets in bundled files
- [ ] Directory name === `name` field

### Quality

- [ ] Description alone determines relevance
- [ ] Tier 2 alone enables execution without Tier 3
- [ ] All rules are concrete and actionable
- [ ] Examples show expected output format
