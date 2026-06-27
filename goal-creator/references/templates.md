# `/goal` Templates and Examples

Full template library with production examples. Use the master template in SKILL.md
as default. Select an alternate template below when the use case matches.

## Table of Contents

1. [Minimal / Quick-Start Template](#minimal-template)
2. [Multi-Agent Coordination Template](#multi-agent-template)
3. [Research / Analysis Template](#research-template)
4. [Code / Build Template](#code-template)
5. [Verification / QA Template](#verification-template)
6. [Template Selection Guide](#selection-guide)

---

## Minimal Template {#minimal-template}

Use for: simple tasks, rapid iteration, proof-of-concepts

```markdown
## /goal: [Name]

**Category**: [CATEGORY]

### Objective
[Observable end state in one sentence]

### Success Criteria
- [ ] [Pass/fail condition 1]
- [ ] [Pass/fail condition 2]

### Constraints
- MUST NOT: [forbidden action]
- LIMIT: [quantified bound]

### Output
[Expected format and required elements]
```

**Example - Document Summarization:**

```markdown
## /goal: Summarize Technical Document

**Category**: EXPLORE

### Objective
Produce a structured summary that captures key claims, evidence, and gaps of
any technical document provided as input.

### Success Criteria
- [ ] All major claims are listed with supporting evidence noted
- [ ] Explicit gaps or unsupported assertions are flagged
- [ ] Summary fits within 250 words
- [ ] Original document's confidence in each claim is preserved

### Constraints
- MUST NOT: introduce information not present in the source document
- MUST NOT: change the confidence level of claims (tentative → certain)
- LIMIT: 250 words maximum

### Output
- Format: Bullet list with 3 sections (Key Claims / Evidence / Flagged Gaps)
- Each claim includes a direct quote or section reference from source
```

---

## Multi-Agent Coordination Template {#multi-agent-template}

Use for: workflows involving multiple agents, handoffs, or dependencies

```markdown
## /goal: [Name]

**Category**: [CATEGORY]
**Agent**: [Role name]
**Upstream Dependencies**: [What must be delivered before this starts]
**Downstream Consumers**: [Who uses this goal's output]

### Objective
[Observable end state]

### Input Contract
[What this agent receives from upstream]

### Output Contract
[What this agent delivers to downstream - exact format, schema, file paths]

### Success Criteria
- [ ] [Pass/fail condition 1]
- [ ] [Pass/fail condition 2]

### Constraints
- MUST NOT: [forbidden action]
- MUST: [required characteristic]

### Handoff Criteria
[Exact condition under which output is passed to downstream agent]

### Failure Recovery
[What to do if this goal cannot be met - escalation, fallback, retry]
```

**Example - Code Review Agent:**

```markdown
## /goal: Security Code Review

**Category**: VERIFY
**Agent**: Security Reviewer
**Upstream Dependencies**: Build agent delivers code diff + context
**Downstream Consumers**: Fix agent receives review report

### Objective
Identify all security vulnerabilities in the provided code change and produce
a structured review report that a fix agent can act upon.

### Input Contract
- Code diff (unified diff format)
- File paths and change context
- Related files for cross-reference (if available)

### Output Contract
- Format: Markdown report with sections: Critical / Warning / Info
- Each finding includes: line reference, vulnerability type, explanation, fix suggestion
- File path: `security_review.md`

### Success Criteria
- [ ] All injection vulnerabilities (SQL, XSS, command) are flagged with line refs
- [ ] All auth bypass patterns are identified
- [ ] Every flagged issue includes a concrete fix suggestion
- [ ] False positive rate target: <20%

### Constraints
- MUST NOT: comment on style, naming, or performance unless security-relevant
- MUST NOT: approve code that contains unflagged injection risks
- MUST: distinguish between "exploitable now" vs "defense in depth" suggestions

### Handoff Criteria
Report delivered AND all Critical findings have fix suggestions

### Failure Recovery
If code language is unsupported → flag as "needs human review" with reason
```

---

## Research / Analysis Template {#research-template}

Use for: information gathering, investigation, competitive analysis

```markdown
## /goal: [Name]

**Category**: EXPLORE
**Research Question**: [Specific question to answer]
**Confidence Target**: [Minimum confidence level for claims]

### Objective
[Observable end state: what knowledge artifact must exist]

### Success Criteria
- [ ] [Evidence-backed claim count]
- [ ] [Source quality threshold]
- [ ] [Gaps and limitations documented]
- [ ] [Contradictions flagged]

### Constraints
- MUST NOT: fabricate sources, statistics, or quotes
- MUST NOT: present opinion as fact
- MUST: cite every claim with source URL and date
- MUST: flag confidence level for each finding (high/medium/low)
- LIMIT: [scope boundaries - time period, geography, source types]

### Output
- Format: Structured markdown with sections per major finding
- Each claim: Claim / Source / URL / Date / Excerpt / Confidence
- Separate "Gaps" section for unanswered questions
- Separate "Conflicts" section for contradictory findings

### Verification Method
Independent checker reads sources and confirms claims match excerpts
```

**Example - Market Research:**

```markdown
## /goal: Competitive Landscape Analysis

**Category**: EXPLORE
**Research Question**: Who are the top 5 competitors in [MARKET] and what are their pricing, positioning, and feature differentiators?
**Confidence Target**: High confidence for pricing (public sources), Medium for positioning (inferred)

### Objective
Deliver a comparison table of 5 competitors with verifiable pricing data,
positioning statements, and feature matrix backed by source citations.

### Success Criteria
- [ ] 5 named competitors with source-verified pricing
- [ ] Positioning statement for each (quoted or clearly inferred with rationale)
- [ ] Feature comparison matrix with at least 8 dimensions
- [ ] All claims cite source URL + access date
- [ ] "Unknown" explicitly stated for unverifiable items (no fabrication)
- [ ] Gaps section lists what information could not be found

### Constraints
- MUST NOT: fabricate pricing - use "not publicly disclosed" if unavailable
- MUST NOT: present competitor marketing claims as objective fact
- MUST: distinguish between "has feature" (confirmed) vs "claims to have" (marketing)
- MUST: note data collection date for time-sensitive data
- LIMIT: Sources from last 12 months only for pricing/features
- LIMIT: Public sources only - no insider or paywalled information

### Output
- Section 1: Summary Table (competitor × dimension matrix)
- Section 2: Deep Dive per competitor (2-3 paragraphs each)
- Section 3: Feature Matrix (markdown table)
- Section 4: Sources (full bibliography)
- Section 5: Gaps and Limitations
- Section 6: Confidence Summary

### Verification Method
Reviewer spot-checks 3 random claims against cited sources

### Failure Modes to Prevent
- Fabricated pricing: always use official sources or "unknown"
- Outdated data: enforce 12-month limit, note access date
- Marketing-as-fact: flag source type for every claim
```

---

## Code / Build Template {#code-template}

Use for: code generation, implementation, technical construction

```markdown
## /goal: [Name]

**Category**: BUILD
**Language/Stack**: [Specific tech stack]
**Existing Context**: [What code/assets already exist]

### Objective
[Observable end state: what artifact must exist and how it must behave]

### Success Criteria
- [ ] [Functional requirement 1 with testable condition]
- [ ] [Functional requirement 2 with testable condition]
- [ ] [Quality gate: lint, type check, tests pass]
- [ ] [Specific edge case handled]

### Constraints
- MUST NOT: [forbidden pattern - e.g., use eval(), skip auth, hardcode secrets]
- MUST: [required pattern - e.g., TypeScript strict mode, input validation, error handling]
- LIMIT: [quantified bound - e.g., bundle size, response time, line count]

### Output
- File paths and naming conventions
- Required function signatures / API shapes
- Test file locations

### Verification Method
- Lint check: [command]
- Type check: [command]
- Test run: [command with coverage threshold]
- Manual check: [specific behavior to verify]

### Failure Modes to Prevent
- [Failure mode]: [prevention]
```

**Example - API Endpoint:**

```markdown
## /goal: Implement User Registration API

**Category**: BUILD
**Language/Stack**: TypeScript, Express, Zod validation
**Existing Context**: Auth module exists at `src/auth/` with User model

### Objective
Create a POST /api/register endpoint that accepts validated user data,
creates a database record, returns a JWT, and handles all error cases securely.

### Success Criteria
- [ ] Endpoint accepts {email, password, name} and returns {token, user}
- [ ] Input validated with Zod - rejects invalid email, short password (<8 chars), missing fields
- [ ] Password hashed with bcrypt (12 rounds) before storage
- [ ] Returns 409 for duplicate email with generic message (no enumeration)
- [ ] Returns 500 with sanitized error (no stack trace to client)
- [ ] JWT expires in 24h, signed with process.env.JWT_SECRET
- [ ] All tests pass with ≥80% branch coverage
- [ ] ESLint and TypeScript strict mode pass with zero warnings

### Constraints
- MUST NOT: store plaintext passwords
- MUST NOT: return detailed error messages to client (security leak)
- MUST NOT: allow email enumeration via error messages
- MUST: validate ALL inputs before database touch
- MUST: log registration attempts with timestamp and IP

### Output
- `src/auth/register.ts` - route handler
- `src/auth/register.test.ts` - test suite
- `src/auth/schema.ts` - Zod schemas (if not existing)

### Verification Method
```bash
npm run lint        # zero warnings
npm run typecheck   # strict mode passes
npm test -- src/auth/register.test.ts  # coverage ≥80%
curl -X POST http://localhost:3000/api/register \
  -H "Content-Type: application/json" \
  -d '{"email":"test@example.com","password":"secure123","name":"Test"}'
```

### Failure Modes to Prevent
- SQL injection: use parameterized queries/ORM, never string interpolation
- Timing attacks: use constant-time comparison for existing user check
- Brute force: rate limiting handled at middleware layer (already exists)
```

---

## Verification / QA Template {#verification-template}

Use for: quality assurance, review, validation, audit tasks

```markdown
## /goal: [Name]

**Category**: VERIFY
**Subject**: [What is being verified]
**Reviewer Role**: [Independent perspective required]

### Objective
[Observable end state: what verification artifact confirms quality]

### Review Criteria (rubric)
| Dimension | Weight | Pass Threshold |
|-----------|--------|----------------|
| [Dimension 1] | [X%] | [Specific threshold] |
| [Dimension 2] | [X%] | [Specific threshold] |

### Constraints
- MUST NOT: approve if any Critical finding is unaddressed
- MUST: provide specific, actionable feedback (not "improve this")
- MUST: distinguish Critical / Warning / Info severity

### Output
- Structured review with per-criterion scoring
- Specific issues with line/section references
- Actionable fix suggestions for every Critical/Warning

### Verification Method
[How a third party confirms the review was thorough]
```

**Example - Code Review:**

```markdown
## /goal: Frontend Code Quality Review

**Category**: VERIFY
**Subject**: React component PR
**Reviewer Role**: Senior Frontend Engineer (critical eye, not rubber stamp)

### Objective
Produce a structured review that ensures the code is correct, maintainable,
accessible, and consistent with project standards before merge.

### Review Criteria (rubric)
| Dimension | Weight | Pass Threshold |
|-----------|--------|----------------|
| Correctness | 30% | No bugs, handles edge cases |
| Accessibility | 20% | axe-core zero violations |
| Performance | 15% | No unnecessary re-renders |
| Maintainability | 20% | Clear naming, reasonable complexity |
| Style Consistency | 15% | Matches project patterns |

### Constraints
- MUST NOT: approve code with unaddressed Critical findings
- MUST NOT: use vague feedback like "consider improving" - be specific
- MUST: test accessibility with axe-core or equivalent mentally
- MUST: check for common React anti-patterns (key index, effect deps, state sync)
- MUST: verify error boundaries and loading states exist

### Output
```
## Review: [PR Title]

### Scores
| Dimension | Score | Notes |
|-----------|-------|-------|
| ... | ... | ... |

### Critical (must fix)
1. [File:Line] - [Issue] - [Fix suggestion]

### Warnings (should fix)
1. [File:Line] - [Issue] - [Fix suggestion]

### Info (optional)
1. [Observation with context]

### Verdict: [APPROVE / CHANGES_REQUESTED / NEEDS_HUMAN_REVIEW]
```

### Verification Method
Another reviewer reads the PR and the review - confirms Critical findings are
real, suggestions are actionable, and scoring is consistent.
```

---

## Template Selection Guide {#selection-guide}

| Situation | Use This Template |
|-----------|-------------------|
| Single agent, simple task, fast turnaround | Minimal |
| Multiple agents with handoffs | Multi-Agent Coordination |
| Research, analysis, investigation | Research / Analysis |
| Writing code, building features | Code / Build |
| Reviewing, testing, auditing | Verification / QA |
| Goal involves both building AND verifying | Multi-Agent Coordination |
| First draft - will iterate | Minimal |
| Production workflow - high stakes | Full master template from SKILL.md |
