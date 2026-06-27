# Workflow-Specific Guidance

Specialized guidance for using `/goal` directives in specific workflow types.
Each section covers: unique quality criteria, common failure modes, stage-specific
goal patterns, and production examples.

## Table of Contents

1. [Website Building Workflows](#website-building)
2. [Build / CI-CD Workflows](#build-cicd)
3. [Research and Analysis Workflows](#research)
4. [Product Development Workflows](#product)

---

## Website Building Workflows {#website-building}

### Unique Quality Criteria

Six dimensions apply specifically to website-building goals:

| Dimension | How to Verify | Common Failure |
|-----------|---------------|----------------|
| **Responsive** | Test at 320px, 768px, 1024px, 1440px | Breaks on mobile, overflow issues |
| **Accessible** | axe-core zero violations, Lighthouse ≥ 90 | Missing alt text, poor contrast |
| **Valid** | W3C validator pass, no console errors | Invalid HTML, broken CSS |
| **Performant** | Lighthouse performance ≥ 85 | Unoptimized images, render-blocking |
| **Semantic** | Proper heading hierarchy, landmark regions | div soup, wrong elements |
| **Consistent** | Matches design system/tokens | Style drift, hardcoded values |

### Five-Layer Quality Gate Stack

Apply these gates in order for every website build:

1. **Lint**: ESLint/Prettier pass, zero warnings
2. **Type check**: TypeScript strict mode, no `any` fallbacks
3. **Security**: No hardcoded secrets, no inline event handlers with user input
4. **Test coverage**: Visual + functional tests, accessibility tests
5. **Agentic check**: Responsive behavior, cross-browser compatibility

### Web-Specific Failure Modes

| Failure Mode | Prevention Strategy |
|-------------|---------------------|
| Hallucinated imports | Lint check after every file generation |
| Type drift to `any` | Mandate `--strict` mode, zero tolerance |
| Skipped responsive | Require breakpoint testing at 320/768/1024px |
| Accessibility violations | Require axe-core zero violations |
| Hardcoded colors/fonts | Enforce design token usage |
| Missing error states | Goal must include loading/error/empty states |
| Poor semantic HTML | Require proper elements (nav, main, article, etc.) |

### Stage-Specific Goals for Web Dev

**Design Phase Goals**:
```markdown
### Constraints
- MUST: use Auto Layout equivalent (flex/grid)
- MUST: target WCAG AA from the start (contrast ratios ≥ 4.5:1)
- MUST: define design tokens (colors, spacing, typography) before components
- MUST NOT: use absolute positioning for responsive elements
```

**Implementation Phase Goals**:
```markdown
### Constraints
- MUST: TypeScript strict mode, zero lint warnings
- MUST: typed props for all components, no `any`
- MUST: responsive at all defined breakpoints
- MUST: include loading, error, and empty states
- MUST NOT: skip accessibility (alt text, focus states, ARIA where needed)
```

**Testing Phase Goals**:
```markdown
### Success Criteria
- [ ] axe-core accessibility scan: zero violations
- [ ] Lighthouse CI: Performance ≥ 85, Accessibility ≥ 90
- [ ] Visual regression: all breakpoints match reference
- [ ] Functional tests: user flows pass end-to-end
- [ ] Cross-browser: Chrome, Firefox, Safari current versions
```

### Example: Landing Page Build Goal

```markdown
## /goal: Build Landing Page

**Category**: BUILD
**Stack**: React 18, TypeScript, Tailwind CSS, Vite

### Objective
A responsive landing page exists at `src/pages/Landing.tsx` that matches
the approved Figma design, passes all quality gates, and handles all states.

### Success Criteria
- [ ] Pixel match to Figma within 4px tolerance (verified via screenshot diff)
- [ ] Responsive: 320px, 768px, 1024px, 1440px breakpoints
- [ ] axe-core: zero accessibility violations
- [ ] Lighthouse: Performance ≥ 85, Accessibility ≥ 90, SEO ≥ 90
- [ ] All interactive elements have hover/focus/active states
- [ ] Loading state shown during data fetch, error state on failure
- [ ] Zero ESLint warnings, TypeScript strict mode passes

### Constraints
- MUST NOT: use inline styles or hardcoded values - use Tailwind classes only
- MUST NOT: skip responsive breakpoints - mobile-first approach
- MUST NOT: use `any` types or `@ts-ignore`
- MUST: use semantic HTML (nav, main, section, footer - not div soup)
- MUST: lazy-load images below the fold
- MUST: include proper meta tags (title, description, OG tags)
- LIMIT: First Contentful Paint < 1.5s, Largest Contentful Paint < 2.5s

### Output
- `src/pages/Landing.tsx` - main page component
- `src/components/landing/*.tsx` - section components
- `src/hooks/useLandingData.ts` - data fetching hook

### Verification Method
```bash
npm run lint
npm run typecheck
npm run test:axe
npm run lighthouse
```
Manual check: resize browser to all 4 breakpoints, verify no overflow.

### Failure Modes to Prevent
- **Layout shift**: specify image dimensions, use aspect-ratio
- **Unstyled flash**: ensure CSS loads before content render
- **Broken navigation**: verify all links have valid href targets
- **Missing analytics**: ensure tracking events fire on CTA clicks
```

---

## Build / CI-CD Workflows {#build-cicd}

### Unique Quality Criteria

| Dimension | How to Verify | Common Failure |
|-----------|---------------|----------------|
| **Build** | Clean compile, zero errors | Type errors, missing deps |
| **Test** | All tests pass, coverage ≥ threshold | Flaky tests, low coverage |
| **Security** | No vulns in deps, no secrets leaked | Outdated packages, hardcoded keys |
| **Deploy** | Zero-downtime, rollback < 5 min | Failed deploy, no rollback |
| **Monitor** | Health checks pass, alerts fire | Silent failures, missing alerts |

### CI-Specific Constraints

```markdown
### Constraints
- MUST: build pass with zero warnings (treat warnings as errors)
- MUST: all tests pass before merge (no skipping)
- MUST: dependency audit pass (`npm audit --audit-level=high`)
- MUST NOT: commit secrets - pre-commit hook blocks
- MUST NOT: merge with failing CI
- LIMIT: build time < 5 minutes for fast feedback
- LIMIT: test coverage ≥ 80% for new code
```

---

## Research and Analysis Workflows {#research}

### Unique Quality Criteria

| Dimension | How to Verify | Common Failure |
|-----------|---------------|----------------|
| **Source quality** | All claims cite reputable sources | Anonymous blogs, outdated data |
| **Coverage** | Research question fully addressed | Partial answer, missed angles |
| **Accuracy** | Key claims spot-checked against sources | Misrepresentation, fabrication |
| **Objectivity** | Multiple viewpoints represented | Cherry-picked evidence |
| **Currency** | Sources within acceptable window | Outdated statistics |

### Research-Specific Constraints

```markdown
### Constraints
- MUST NOT: fabricate sources, statistics, or quotes
- MUST NOT: present opinion as fact
- MUST: cite every claim with source URL and access date
- MUST: flag confidence level per finding (high/medium/low)
- MUST: document gaps - what could not be found
- MUST: present contradictory evidence when it exists
- LIMIT: Sources from last [N] months only (define per topic)
- LIMIT: Primary/authoritative sources preferred over secondary
```

### Research Verification Method

```markdown
### Verification Method
1. Spot-check: reviewer picks 3 random claims, verifies against cited sources
2. Source audit: all sources are real, accessible, and match cited dates
3. Coverage check: research question is fully answered, not partially
4. Bias scan: multiple perspectives represented, not just confirming evidence
```

---

## Product Development Workflows {#product}

### Unique Quality Criteria

| Dimension | How to Verify | Common Failure |
|-----------|---------------|----------------|
| **User value** | Solves stated user problem | Feature no one asked for |
| **Feasibility** | Can be built with available resources | Over-engineered, unbuildable |
| **Testability** | Acceptance criteria are verifiable | Vague "improve UX" goals |
| **Measurability** | Success metrics defined upfront | No way to know if it worked |
| **Scope control** | In/out of scope explicit | Scope creep, never-ending |

### Product-Specific Goal Pattern

```markdown
## /goal: [Feature Name]

**Category**: [DEFINE / BUILD]
**User Story**: As a [user], I want [goal], so that [benefit]
**Priority**: [P0 critical / P1 important / P2 nice-to-have]
**Success Metric**: [Measurable outcome - adoption, time saved, error reduction]

### Objective
[Observable: feature shipped, metric target met]

### Success Criteria
- [ ] Feature works as specified for primary use case
- [ ] Edge cases [list] handled gracefully
- [ ] Success metric improves by [target] within [timeframe]
- [ ] No regression in [critical existing metric]
- [ ] Documentation updated

### Constraints
- MUST NOT: break existing user flows
- MUST: include analytics instrumentation
- MUST: have feature flag for gradual rollout
- LIMIT: scope to MVP - nice-to-haves in separate goal
```
