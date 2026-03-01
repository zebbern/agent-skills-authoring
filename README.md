---
name: agent-skills-authoring
description: >-
  Guides agents through creating, validating, and optimizing Agent Skills (SKILL.md files)
  from user requests. Covers all YAML frontmatter fields, body structure, directory layout,
  progressive disclosure, scripts, references, and quality patterns. Use when a user asks
  you to create, edit, audit, or improve an Agent Skill, even if they don't use the word
  "skill" — any request about reusable agent instructions, workflow packaging, or capability
  extension should trigger this skill.
license: MIT
compatibility: >-
  Any AI agent generating SKILL.md files. Follows the Agent Skills specification (agentskills.io).
metadata:
  spec-version: "1.0"
  last-verified: "2026-03-01"
  source: "https://agentskills.io/specification"
  audience: "ai-agents"
allowed-tools: Bash(skills-ref:*) Read Write
---

# Agent Skills Authoring Guide

You are reading this because a user wants you to create, edit, or improve an Agent Skill.
This is your authoritative reference for producing high-quality SKILL.md files that conform
to the Agent Skills specification and follow patterns proven in Anthropic's production skills.

**Your goal:** Transform the user's request into the best possible SKILL.md — one that any
consuming agent can discover, activate, and apply effectively.

---

## 1. What Agent Skills Are

An Agent Skill is a portable, model-agnostic instruction package stored as a directory.
It tells an AI agent **what to do**, **when to activate**, and **how to behave** for a
specific domain. Skills are consumed at inference time — not code libraries, not prompt
templates, not fine-tuning datasets.

- Plain text Markdown, no compilation required
- One skill per directory, one `SKILL.md` per skill
- Portable across agents, platforms, and models
- Discoverable via description keywords — agents match user requests against descriptions
- Validated via `skills-ref validate`

---

## 2. Directory Structure

```
skill-name/
├── SKILL.md          # REQUIRED — frontmatter + instructions
├── scripts/          # Optional — executable code for deterministic tasks
│   └── extract.py
├── references/       # Optional — docs loaded into context on demand
│   ├── REFERENCE.md
│   └── domain.md
└── assets/           # Optional — templates, data files, schemas
    └── template.json
```

**Rules:**

- The directory name must match the `name` field in frontmatter exactly
- `SKILL.md` is the only required file — everything else is optional
- `scripts/` — executable code agents can run. Should be self-contained, include
  helpful error messages, and handle edge cases. Languages depend on agent platform
- `references/` — additional documentation loaded on demand. Keep individual files
  focused. For large reference files (>300 lines), include a table of contents at top
- `assets/` — static resources like templates, configuration files, lookup tables, schemas

**Domain organization:** When a skill supports multiple variants, use references:

```
cloud-deploy/
├── SKILL.md           # Workflow + variant selection logic
└── references/
    ├── aws.md
    ├── gcp.md
    └── azure.md
```

The agent reads only the relevant reference file, saving context.

---

## 3. YAML Frontmatter — Complete Field Reference

The spec defines exactly **six fields**. No others exist. Use `---` delimiters.

### `name` (REQUIRED)

Unique identifier. 1–64 characters. Lowercase alphanumeric + hyphens only.
No leading/trailing/consecutive hyphens. Must match the parent directory name.

```yaml
name: api-design-patterns
```

Derive from the skill's primary function. Be specific (`react-component-gen` not
`component-gen`) and concise (`git-workflow` not `git-version-control-management`).

### `description` (REQUIRED)

Natural-language summary for discoverability. 1–1,024 characters. Plain text only.
This is the primary triggering mechanism — agents match user requests against descriptions
to decide which skills to load.

```yaml
description: >-
  Guides agents through REST API endpoint design including naming conventions,
  HTTP methods, status codes, pagination, and error responses. Use when designing
  API endpoints, reviewing APIs, or generating OpenAPI specs, even if the user
  doesn't explicitly mention "REST" or "API design."
```

**Why the description matters so much:** Claude and other agents tend to _undertrigger_
skills — they don't use skills when they'd be helpful. To combat this, make descriptions
slightly "pushy." Include both what the skill does AND specific contexts that should
trigger it. List keywords a user might say. Add "even if they don't explicitly ask for X"
where appropriate.

Good pattern: Start with an action verb → list capabilities → state "Use when..." →
add trigger keywords. Aim for 200-400 characters.

### `license` (OPTIONAL)

SPDX identifier or reference to a bundled license file. Omit if unknown.

```yaml
license: Apache-2.0
```

### `compatibility` (OPTIONAL)

Environment requirements. 1–500 characters. Plain text. Only include if the skill
has genuine environment requirements — most skills don't need this.

```yaml
compatibility: Requires git, docker, jq, and access to the internet
```

### `metadata` (OPTIONAL)

Arbitrary key-value map. Keys and values must be strings. Use reasonably unique key
names to avoid collisions across skills.

```yaml
metadata:
  author: "your-org"
  version: "1.0"
  category: "code-quality"
```

### `allowed-tools` (OPTIONAL, EXPERIMENTAL)

Space-delimited list of pre-approved tools. Support varies between agent platforms.
Use scoped format where the platform supports it.

```yaml
allowed-tools: Bash(git:*) Bash(jq:*) Read Write
```

---

## 4. Body Content — Writing Effective Instructions

Everything after the closing `---` is the body. Free-form Markdown, no format
restrictions from the spec. The following guidance comes from Anthropic's production
skills and their official skill-creator.

### Recommended Structure

```markdown
# [Skill Name]

[1-3 sentence overview of what this enables]

## When to use this skill

[Trigger conditions — what user requests or contexts activate this]

## How to [primary action]

[Step-by-step instructions]

## Examples

[Input/output examples showing expected behavior]

## Common edge cases

[Boundary conditions and how to handle them]

## Guidelines

[Key principles, organized by topic if needed]
```

### Writing Style

**Explain WHY, not just WHAT.** Anthropic's guidance: "Try to explain to the model
why things are important in lieu of heavy-handed musty MUSTs." When agents understand
the reasoning behind a rule, they apply it more flexibly and correctly across novel
situations. Rigid imperative-only instructions lead to brittle, over-literal behavior.

Good: "Keep descriptions under 400 characters because agents truncate longer text
during discovery, so front-load the most important keywords."

Less effective: "[P0-MUST] Description MUST be under 400 characters."

**Use theory of mind.** Write instructions that are general enough to handle situations
you haven't explicitly covered. Think about what context the consuming agent will have
and what it won't.

**Keep it scannable.** Use headers, numbered steps, and code blocks. But don't fragment
into so many tiny sections that the flow of reasoning is lost.

**Match the domain's tone.** A skill for creative writing should feel different from one
for security auditing. Production skills in the Anthropic repo range from casual
(`"Cool? Cool."`) to structured (phased workflows with checklists).

### Progressive Disclosure (Token Budget)

Skills use a three-tier loading system for efficient context use:

| Tier | Content                          | Budget      | Loaded When                |
| ---- | -------------------------------- | ----------- | -------------------------- |
| 1    | Frontmatter (name + description) | ~100 words  | Always — at agent startup  |
| 2    | SKILL.md body                    | < 500 lines | When skill activates       |
| 3    | scripts/, references/, assets/   | As needed   | On demand during execution |

Keep your SKILL.md body under 500 lines. If approaching this limit, move detailed
reference material into `references/` and add clear pointers about when to read them.
Scripts execute without being loaded into context, so they don't count toward the limit.

### File References

Reference bundled files using standard Markdown links with relative paths:

```markdown
See [the reference guide](references/REFERENCE.md) for detailed API documentation.
Run the extraction script: `scripts/extract.py`
Load [the TypeScript patterns](references/typescript.md) for TS-specific guidance.
```

Keep file references one level deep from SKILL.md. Avoid deeply nested reference chains.
Put core rules in the SKILL.md body — don't rely on agents loading referenced files for
essential instructions.

---

## 5. Creating a Skill from a User Request

### Step 1: Capture Intent

Start by understanding what the user actually needs. The conversation may already contain
a workflow they want to capture ("turn this into a skill"). If so, extract from context:

- What should this skill enable an agent to do?
- When should it trigger? (what user phrases, tasks, or contexts?)
- What's the expected output format?
- Are there edge cases or constraints?
- Does this need test cases? (Skills with objectively verifiable outputs benefit from
  testing. Skills with subjective outputs like writing style often don't.)

Ask clarifying questions if the domain, scope, or constraints are ambiguous. Research
available docs and similar skills to come prepared.

### Step 2: Choose the Name

Derive from the primary domain + action: `{domain}-{action}` or `{domain}-{qualifier}`.
Validate: lowercase, hyphens only, 1-64 chars, no leading/trailing/consecutive hyphens.
The name becomes the directory name — they must match.

### Step 3: Write the Description

This is the most important field. Start with an action verb, list capabilities, state
"Use when...", and include trigger keywords. Make it slightly pushy — err on the side of
over-triggering rather than under-triggering. Aim for 200-400 characters.

### Step 4: Set Optional Fields

- **license:** User-specified, or `MIT`/`Apache-2.0`, or omit
- **compatibility:** Only if the skill has real environment requirements
- **metadata:** Add `author`, `version`, `category` at minimum
- **allowed-tools:** List tools using scoped format where supported

### Step 5: Write the Body

Start with a draft. Then look at it with fresh eyes and improve it.

- Open with 1-3 sentence overview
- Add "When to use this skill" with clear trigger conditions
- Write step-by-step instructions organized by phases or topics
- Include input/output examples
- Cover common edge cases
- Add guidelines that explain _why_ not just _what_

### Step 6: Iterate

The best skills are not written in one pass. The recommended loop:

1. Write a draft
2. Create 2-3 realistic test prompts — things a real user would say
3. Run an agent with the skill on those prompts
4. Evaluate outputs (qualitative review + any applicable assertions)
5. Rewrite based on what you learned
6. Repeat until satisfied, then expand the test set

### Step 7: Validate

- Directory name matches `name` field exactly
- Description: 1-1,024 chars, starts with action verb, includes trigger keywords
- All file references resolve to files that exist
- SKILL.md body is under 500 lines
- Run `skills-ref validate ./skill-directory` if available
- Run `skills-ref to-prompt ./skill-directory` to preview the XML agents see

---

## 6. Quality Patterns from Production Skills

These patterns appear across Anthropic's own production skills (79.5K+ stars repo).

### Phased Workflows

Break complex skills into phases the agent can follow sequentially:

```markdown
## Phase 1: Research and Planning

[Understand the domain, gather context]

## Phase 2: Implementation

[Execute the primary task]

## Phase 3: Review and Validate

[Check quality, run validation]
```

### Decision Tables

When agents need to choose between approaches:

| Scenario      | Approach          | Why                              |
| ------------- | ----------------- | -------------------------------- |
| < 100 items   | Inline array      | No pagination overhead           |
| 100-10K items | Cursor pagination | Stable ordering, no offset drift |
| > 10K items   | Keyset pagination | O(1) page fetch, index-friendly  |

### Before/After Examples

Show the transformation clearly:

```markdown
**Before:** `app.get('/getUser', ...)`
**After:** `app.get('/users/:id', ...)`
```

### Defining Output Formats

```markdown
## Report structure

Always use this template:

# [Title]

## Executive summary

## Key findings

## Recommendations
```

### Conditional Paths

When behavior depends on context, use variant sections:

```markdown
### If the project uses TypeScript:

- Use strict mode (`"strict": true`)
- Prefer `interface` over `type` for object shapes

### If the project uses JavaScript:

- Use JSDoc annotations for function signatures
```

---

## 7. Anti-Patterns — What Makes Skills Fail

| Anti-Pattern                  | Why It Fails                                         | Fix                                                     |
| ----------------------------- | ---------------------------------------------------- | ------------------------------------------------------- |
| Vague description             | Agents can't match it to user requests               | Add keyword triggers + "Use when..." + be pushy         |
| Overly rigid rules            | Agents apply rules literally even when inappropriate | Explain _why_ — agents generalize better from reasoning |
| Wall of text                  | Agents lose focus, may truncate                      | Use headers, steps, tables. Stay under 500 lines        |
| Code in description           | Violates spec (plain text only), breaks parsers      | Put all code in the body                                |
| SKILL.md over 500 lines       | Wastes context, agent may not read to the end        | Move detail to `references/` directory                  |
| No examples                   | Agent guesses at expected output format              | Include at least 2-3 input/output examples              |
| Core rules only in references | Agent may not load reference files                   | Put essential rules in SKILL.md body                    |
| Name mismatch                 | `skills-ref validate` fails immediately              | Directory name === `name` field, always                 |
| Generic description           | Doesn't differentiate from similar skills            | Be specific about scope, include trigger phrases        |

---

## 8. Security

- Never include secrets, API keys, or credentials in SKILL.md or bundled files
- Never instruct agents to disable security controls or bypass authentication
- Scripts in `scripts/` should be sandboxed or run with least privileges
- Include input validation guidance for skills handling user-provided data
- Include sanitization guidance for skills generating browser-rendered output
- Flag destructive tools in `allowed-tools` clearly so users can review

---

## 9. Testing

1. **Syntax:** `skills-ref validate ./skill-name` — confirms frontmatter and name match
2. **Prompt preview:** `skills-ref to-prompt ./skill-name` — generates the XML agents see
3. **Activation test:** Give an agent a prompt that SHOULD trigger this skill. Confirm it fires
4. **Negative test:** Give a prompt that should NOT trigger it. Confirm it stays dormant
5. **Quality test:** Have the agent execute the skill. Review whether output follows guidelines
6. **Edge cases:** Try ambiguous or boundary-case prompts

For skills with verifiable outputs, create `evals/evals.json` with test prompts and
assertions. Run with-skill and without-skill variants to measure improvement.

---

## 10. Platform Usage

- **Claude Code:** `/plugin marketplace add anthropics/skills` then `/plugin install <name>@anthropic-agent-skills`
- **Claude.ai:** Pre-built skills available to paid plans. Custom skills uploaded via UI
- **Claude API:** Skills API Quickstart in Anthropic's docs for programmatic loading

---

## 11. Resources

| Resource                                   | URL                                     |
| ------------------------------------------ | --------------------------------------- |
| Agent Skills Specification                 | https://agentskills.io/specification    |
| Anthropic Skills Repository (79.5K+ stars) | https://github.com/anthropics/skills    |
| Agent Skills Registry                      | https://agentskills.io/registry         |
| skills-ref CLI (validate + to-prompt)      | https://agentskills.io/tools            |
| Integration Guide (for agent developers)   | https://agentskills.io/integrate-skills |

---

## 12. Pre-Delivery Checklist

Before delivering any SKILL.md, verify:

### Frontmatter

- [ ] `name`: 1-64 chars, lowercase alphanum + hyphens, matches directory name
- [ ] `description`: 1-1,024 chars, action verb, trigger keywords, "Use when...", slightly pushy
- [ ] Optional fields set only where relevant — no invented fields
- [ ] `allowed-tools` uses scoped format if platform supports it

### Body

- [ ] Opens with 1-3 sentence overview
- [ ] Has trigger section ("When to use this skill" or equivalent)
- [ ] Instructions explain WHY, not just WHAT
- [ ] Includes input/output examples
- [ ] Covers common edge cases
- [ ] Under 500 lines total
- [ ] Pure Markdown — no HTML

### Files

- [ ] All Markdown links to bundled files resolve to real files
- [ ] Large reference files (>300 lines) have a table of contents
- [ ] Scripts are self-contained with helpful error messages
- [ ] No secrets in any file
- [ ] Directory name === `name` field

### Quality

- [ ] Description alone determines relevance (an agent reading only the description can decide)
- [ ] Body alone enables execution (agent shouldn't need references for core workflow)
- [ ] Instructions are general enough to handle situations not explicitly covered
- [ ] A real user would find the skill's output useful, not just spec-compliant
