# Gold Patterns and Anti-Patterns

Complete catalog of proven patterns to follow and common mistakes to avoid when
creating `/goal` directives. Each pattern includes: when to use, how to implement,
example, and rationale.

## Table of Contents

1. [Gold Patterns](#gold-patterns)
2. [Anti-Patterns](#anti-patterns)
3. [Before / After Examples](#before-after)
4. [Quick Reference Card](#quick-reference)

---

## Gold Patterns {#gold-patterns}

### GP1: End-State-First Definition

Define "done" in verifiable terms before describing any process.

**When to use**: All `/goal` directives. This is the highest-impact pattern.

**How to implement**:
1. Write the end state as a concrete, observable condition
2. Add how to verify that condition was reached
3. Only then add constraints and context

**Example**:
```markdown
### Objective
End State: All TODO markers in the codebase are replaced with actionable
GitHub issues, and zero TODOs remain in source code.

### Verification
Run `git grep -r "TODO" src/` and confirm zero matches.
Each issue links back to the original TODO location.
```

**Rationale**: End-state-first eliminates ambiguity about completion. Research
shows that most agent failures stem from unclear "done" conditions, not unclear
instructions.

---

### GP2: Structured Output Contract

Specify exact output format as an interface contract - structure, required fields,
and examples.

**When to use**: When downstream agents, systems, or humans consume the output.

**How to implement**:
1. Define file format (markdown, JSON, etc.)
2. List required sections/fields in order
3. Provide a brief example of correct output

**Example**:
```markdown
### Output Specification
- Format: Markdown with YAML frontmatter
- Required sections: Summary, Findings (numbered), Sources, Gaps
- Each Finding must include: Claim, Evidence, Confidence (high/medium/low)
- File naming: `YYYY-MM-DD-[topic]-research.md`
```

---

### GP3: Constraint-First Safety Bounds

Define what NOT to do before what to do. Constraints prevent the most dangerous
failures.

**When to use**: All production `/goal` directives, especially those handling data,
user input, or external systems.

**How to implement**:
1. List MUST NOT constraints first (safety-critical)
2. List MUST constraints second (required characteristics)
3. List LIMIT constraints last (quantified bounds)

**Example**:
```markdown
### Constraints
- MUST NOT: store or transmit user passwords in plaintext
- MUST NOT: log PII (emails, phone numbers) at INFO level or higher
- MUST: hash passwords with bcrypt before any database operation
- MUST: return generic error messages to client (no stack traces)
- LIMIT: response time < 200ms for 95th percentile
```

---

### GP4: Second-Agent Test

A different agent must be able to verify success from the output alone, without
re-doing the work.

**When to use**: All `/goal` directives. This is the primary test of quality.

**How to implement**:
1. Imagine a different agent receives only the output
2. Ask: could it confirm all success criteria were met?
3. If not, the output specification needs more structure or evidence

**Example**:
```markdown
### Verification Method
An independent reviewer can verify by:
1. Running the provided test command and confirming all pass
2. Checking the output file exists at the specified path
3. Confirming each success criterion has corresponding evidence in the output
```

---

### GP5: BDD-Style Verification (Given/When/Then)

For complex multi-step goals, use Behavior-Driven Development structure.

**When to use**: Complex workflows with clear preconditions and multiple scenarios.

**How to implement**:
- **Given**: starting context and prerequisites
- **When**: the action or event
- **Then**: expected outcome with verifiable conditions

**Example**:
```markdown
### Scenario 1: Valid Registration
- Given: No existing user with email "test@example.com"
- When: POST /api/register with {email, password, name}
- Then: Response is 201, returns {token, user}, password is hashed

### Scenario 2: Duplicate Email
- Given: User with email "exists@example.com" already registered
- When: POST /api/register with same email
- Then: Response is 409, message is "Registration failed" (no enumeration)
```

---

### GP6: Explicit Failure Mode Documentation

List specific failure modes and their prevention strategies.

**When to use**: Any `/goal` where the cost of failure is significant.

**How to implement**:
1. Brainstorm 3-5 ways this goal could fail silently
2. For each: describe the failure mode and how the goal prevents it
3. Ensure prevention is embedded in constraints or success criteria

**Example**:
```markdown
### Failure Modes to Prevent
- **Silent data loss**: if write fails, retry 3× then escalate - never return success
- **Race condition**: use atomic compare-and-swap, verify state before update
- **Injection attack**: all user inputs parameterized, never string-interpolated
```

---

### GP7: Atomic Sub-Goal Decomposition

Break complex goals into independent sub-goals that can be verified separately.

**When to use**: Goals with more than 5 success criteria or multi-phase execution.

**How to implement**:
1. Split into sub-goals at natural handoff points
2. Each sub-goal has its own `/goal` with input/output contracts
3. Parent goal specifies sequencing and dependencies only

---

## Anti-Patterns {#anti-patterns}

### AP1: Vague Aspiration

**Symptom**: "Create something good" / "Make it better" / "Improve quality"

**Why it fails**: Agent cannot determine when it is done. Leads to either
endless refinement or premature stopping.

**Fix**: Replace subjective adjectives with measurable conditions.

### AP2: Over-Specification

**Symptom**: Prescribes every micro-step, leaving no room for agent reasoning.

**Why it fails**: Brittle - breaks when context changes. Wastes agent capability.
Research shows over-specification drops accuracy by ~19%.

**Fix**: Specify the outcome, not the process. Move procedural details to context
or examples.

### AP3: Under-Specification

**Symptom**: Missing output format, no constraints, no verification method.

**Why it fails**: Agent invents its own standards, producing inconsistent output.

**Fix**: Add output contract section with format + required elements.

### AP4: Conflicting Signals

**Symptom**: Multiple incompatible priorities without explicit ranking.

**Why it fails**: Agent oscillates between objectives, satisfies none fully.

**Fix**: Add explicit priority order. "Priority: 1. Security, 2. Performance, 3. Conciseness"

### AP5: No Termination Condition

**Symptom**: Agent does not know when to stop - no explicit stop condition.

**Why it fails**: Infinite loops or runaway token consumption.

**Fix**: Add explicit stop conditions to every BUILD and EXPLORE goal.

### AP6: Sycophancy Trap

**Symptom**: Vague praise-seeking language: "ensure quality," "be thorough."

**Why it fails**: RLHF-trained models trend toward agreement and flattery. Vague
positive instructions produce verbose, low-substance output.

**Fix**: Replace with objective, verifiable criteria.

### AP7: Hidden Assumptions

**Symptom**: Assumes agent knows context that was never stated.

**Why it fails**: Agent works with wrong assumptions, produces subtly incorrect output.

**Fix**: Make all prerequisites and assumptions explicit in the Context section.

### AP8: Brittle References

**Symptom**: Hardcoded dates, names, paths, or one-time context.

**Why it fails**: Goal becomes unusable for future runs.

**Fix**: Use parameterized placeholders: `[PROJECT_NAME]`, `[DATE]`, `[ENV]`

---

## Before / After Examples {#before-after}

### Example 1: Vague → Specific

**BEFORE** (Vague Aspiration + Sycophancy Trap):
```markdown
Goal: Write a good README for this project.
Make it comprehensive and professional.
```

**AFTER** (End-State-First + Structured Output):
```markdown
## /goal: Create Project README

**Category**: BUILD

### Objective
A README.md exists that enables a new developer to install, configure, and run
the project within 15 minutes without asking questions.

### Success Criteria
- [ ] Installation section has copy-paste commands that work on macOS and Linux
- [ ] Configuration section lists ALL required env vars with descriptions
- [ ] Usage section shows the most common command with expected output
- [ ] Troubleshooting section covers the 3 most common setup errors
- [ ] "Quick Start" subsection at top gets user running in < 5 minutes

### Constraints
- MUST NOT: include internal-only or deprecated features
- MUST: use the same terminology as the codebase (check src/ for naming)
- LIMIT: ≤ 150 lines (force conciseness)

### Output
- Single file: `README.md`
- Format: Markdown with code blocks for all commands
```

---

### Example 2: Over-Specified → Outcome-Focused

**BEFORE** (Over-Specification - prescribes process):
```markdown
Goal: Generate a CSV report.
Step 1: Open the data file
Step 2: Read column headers
Step 3: Count rows where status="active"
Step 4: Calculate average value column
Step 5: Write results to report.csv with headers "Metric,Value"
```

**AFTER** (Outcome-Focused with verification):
```markdown
## /goal: Generate Usage Report

**Category**: BUILD

### Objective
A CSV file exists at `reports/usage-YYYY-MM-DD.csv` containing aggregated
usage metrics for the current month.

### Success Criteria
- [ ] File contains exactly 4 rows: total users, active users, avg session time, total events
- [ ] All values are numeric (no "N/A" or text in value column)
- [ ] Date range in filename matches data date range in file
- [ ] File uses UTF-8 encoding, comma-delimited, with header row

### Constraints
- MUST NOT: include personally identifiable information
- MUST: use data from `events` table only (not `users` table for counts)
- LIMIT: query must complete in < 30 seconds

### Output
- Format: CSV with headers `metric,value,date_range`
- Location: `reports/usage-YYYY-MM-DD.csv`

### Verification Method
Open CSV and confirm 4 data rows + correct date range in filename
```

---

### Example 3: No Constraints → Safety-Constrained

**BEFORE** (Missing safety constraints):
```markdown
Goal: Summarize the user feedback emails and send the summary to the team.
```

**AFTER** (Constraint-First Safety):
```markdown
## /goal: Summarize User Feedback

**Category**: EXPLORE

### Objective
A structured summary of user feedback emails exists, highlighting top themes,
sentiment trends, and actionable items - without exposing individual identities.

### Success Criteria
- [ ] Top 5 themes identified with occurrence counts
- [ ] Sentiment distribution (positive/neutral/negative) with percentages
- [ ] 3-5 actionable items extracted
- [ ] No individual user names or contact info in output

### Constraints
- MUST NOT: include sender names, email addresses, or identifying details
- MUST NOT: quote feedback verbatim if it contains profanity (paraphrase instead)
- MUST: aggregate counts require minimum 3 mentions to be a "theme"
- MUST: flag any feedback mentioning security incidents or data breaches separately

### Output
- Format: Markdown with sections (Themes / Sentiment / Action Items / Escalations)
- Stored at: `reports/feedback-summary-YYYY-MM-DD.md`
```

---

### Example 4: Missing Verification → Verifiable

**BEFORE** (No way to verify):
```markdown
Goal: Refactor the auth module to be cleaner.
```

**AFTER** (Second-Agent Test passes):
```markdown
## /goal: Refactor Auth Module

**Category**: BUILD

### Objective
The auth module passes all existing tests, has zero lint warnings, cyclomatic
complexity ≤ 10 per function, and all public functions have JSDoc comments.

### Success Criteria
- [ ] All existing tests pass: `npm test -- src/auth/`
- [ ] Zero ESLint warnings: `npm run lint -- src/auth/`
- [ ] Cyclomatic complexity ≤ 10 for every function (check with `npm run complexity`)
- [ ] JSDoc comments on all exported functions with @param and @returns
- [ ] No behavioral changes - existing test suite passes without modification

### Constraints
- MUST NOT: change any function signatures (breaking change)
- MUST NOT: modify any test files (except renaming for clarity)
- MUST: preserve all existing error handling behavior

### Verification Method
```bash
npm test -- src/auth/        # all pass
npm run lint -- src/auth/    # zero warnings
npm run complexity           # all ≤ 10
```
Independent reviewer confirms test suite unchanged and all checks pass.
```

---

## Quick Reference Card {#quick-reference}

**Every `/goal` needs these 5 things:**
1. Observable end state (not process)
2. Pass/fail success criteria (3-7 items)
3. MUST NOT constraints (at least 1)
4. Output format specification
5. How to verify without re-doing

**Before delivering, confirm:**
- Second agent could verify from output alone? (GP4)
- No vague adjectives ("good," "quality," "thorough")?
- No process prescription where outcome will do?
- All quantified where possible?
- Safety constraints first in constraint list?

**Most common mistakes:**
1. Vague aspiration (no measurable criteria)
2. Over-specification (prescribes process not outcome)
3. Missing output format
4. No termination condition
5. Hidden assumptions about context
