# High-Quality Agent Skills

A curated collection of reusable agent skills built for the [Agent Skills open standard](https://agentskills.io). Each skill is self-contained, well-documented, and designed to be loaded on demand by compatible agents.

## Repository Layout

```
<skill-name>/
├── SKILL.md          # Skill definition with YAML frontmatter + instructions
├── references/       # Optional deep-dive reference files
├── scripts/          # Optional executable helpers
└── assets/           # Optional templates, schemas, or data files
```

## Available Skills

- [`goal-creator`](./goal-creator) — Create reusable, testable, outcome-oriented `/goal` directives.

## Usage

Compatible agent platforms can load skills directly from the `.claude-plugin/plugin.json` manifest. Point your tool at the repository root and it will discover every listed skill automatically.

## Contributing

Add new skills as top-level directories. Keep `SKILL.md` focused, follow the existing frontmatter style, and include references only when they meaningfully extend the core instructions.
