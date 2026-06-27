# Category Patterns by Workflow Stage

Each `/goal` belongs to a workflow category. This guide defines the 8 categories,
their purpose, structural characteristics, templates, and stage-transition criteria.

## Table of Contents

1. [Category Overview](#overview)
2. [EXPLORE - Discovery](#explore)
3. [DEFINE - Scoping](#define)
4. [PLAN - Design](#plan)
5. [BUILD - Development](#build)
6. [VERIFY - Testing](#verify)
7. [DEPLOY - Release](#deploy)
8. [MONITOR - Operations](#monitor)
9. [REFLECT - Learning](#reflect)
10. [Stage Transitions](#transitions)

---

## Category Overview {#overview}

| Category | Stage | Verb Family | Output Type | When to Use |
|----------|-------|-------------|-------------|-------------|
| **EXPLORE** | Discovery | explore, investigate, research, gather | Insights, findings | Gathering information, learning, discovering |
| **DEFINE** | Scoping | define, scope, frame, specify | Problem statement, requirements | Setting boundaries, specifying requirements |
| **PLAN** | Design | plan, design, architect, select | Blueprint, roadmap | Creating blueprints, selecting approaches |
| **BUILD** | Development | implement, build, develop, create | Artifacts, code, docs | Producing artifacts, writing code/content |
| **VERIFY** | Testing | verify, validate, test, confirm | Test results, evidence | Checking correctness, quality assurance |
| **DEPLOY** | Release | deploy, launch, release, rollout | Live system | Shipping to production |
| **MONITOR** | Operations | monitor, maintain, improve, optimize | Metrics, feedback | Ongoing operations, optimization |
| **REFLECT** | Learning | reflect, evaluate, learn, document | Lessons learned | Post-hoc analysis, knowledge capture |

**Goal Evolution Chain**: Goals transform as work progresses:
- EXPLORE outputs inform DEFINE goals
- DEFINE outputs set boundaries for PLAN goals
- PLAN outputs direct BUILD goals
- BUILD outputs feed VERIFY goals
- VERIFY outputs gate DEPLOY goals
- DEPLOY outputs generate MONITOR goals
- MONITOR outputs trigger REFLECT goals
- REFLECT outputs improve future EXPLORE goals

---

## EXPLORE - Discovery {#explore}

**Purpose**: Gather information, build understanding, discover unknowns.

**Structural Characteristics**:
- Input: Topic, question, or data source
- Output: Structured findings with confidence levels and source citations
- Success criteria: Coverage, accuracy, citation quality, gap identification
- Key constraint: MUST NOT fabricate sources or hallucinate data

**Template**:
```markdown
## /goal: [Name]

**Category**: EXPLORE
**Research Question**: [Specific question]
**Confidence Target**: [Minimum confidence for claims]

### Objective
[Observable knowledge artifact to produce]

### Success Criteria
- [ ] [Coverage criterion: what must be investigated]
- [ ] [Evidence quality: source threshold]
- [ ] [Gap documentation: unknowns explicitly listed]
- [ ] [Contradiction flagging: conflicting findings noted]

### Constraints
- MUST NOT: fabricate sources, statistics, or quotes
- MUST: cite every claim with source URL and date
- MUST: flag confidence (high/medium/low) per finding
- LIMIT: [scope boundaries]

### Output
[Format, sections, required elements]

### Verification Method
[Spot-check methodology]
```

**Example**:
```markdown
## /goal: Investigate API Failure Pattern

**Category**: EXPLORE
**Research Question**: Why are 5% of payment API calls failing intermittently?
**Confidence Target**: High for root cause, Medium for fix recommendation

### Objective
A structured incident analysis document that identifies the root cause of
intermittent payment API failures and provides evidence-backed fix options.

### Success Criteria
- [ ] Failure timestamps correlated with deployment or traffic events
- [ ] Error logs analyzed with top 3 error patterns identified
- [ ] Infrastructure metrics (CPU, memory, DB connections) checked for correlation
- [ ] At least 2 fix options with trade-off analysis
- [ ] All claims cite specific log entries, metrics, or code references

### Constraints
- MUST NOT: blame without evidence
- MUST NOT: recommend changes that lack rollback plan
- MUST: distinguish confirmed findings from hypotheses
- LIMIT: Analysis covers last 14 days only

### Output
- Format: Markdown incident report
- Sections: Summary / Timeline / Evidence / Root Cause / Fix Options / Rollback Plan

### Verification Method
Another engineer reads the report and can locate every cited log entry
```

---

## DEFINE - Scoping {#define}

**Purpose**: Set boundaries, clarify requirements, establish what is in and out of scope.

**Structural Characteristics**:
- Input: Raw requirements, stakeholder input, or exploration findings
- Output: Scoped problem statement with acceptance boundaries
- Success criteria: Clarity, completeness, measurability, stakeholder alignment
- Key constraint: MUST NOT scope creep - boundaries are explicit

**Template**:
```markdown
## /goal: [Name]

**Category**: DEFINE
**Stakeholders**: [Who defined this scope]

### Objective
[A scoped problem statement that is unambiguous and testable]

### Success Criteria
- [ ] Problem statement fits in 2 sentences
- [ ] In-scope items are explicitly listed
- [ ] Out-of-scope items are explicitly listed
- [ ] Success can be verified objectively (no "feel" criteria)
- [ ] Constraints and dependencies are documented

### Constraints
- MUST NOT: include out-of-scope items without explicit stakeholder approval
- MUST: define "not doing" with same precision as "doing"
- MUST: identify at least 3 key assumptions

### Output
[Format: scope document, decision record, or requirements brief]
```

---

## PLAN - Design {#plan}

**Purpose**: Create blueprints, select approaches, design solutions.

**Structural Characteristics**:
- Input: Scoped problem statement
- Output: Design document, architecture decision, or implementation roadmap
- Success criteria: Feasibility, correctness, completeness, risk identification
- Key constraint: MUST NOT commit to unvalidated assumptions

**Template**:
```markdown
## /goal: [Name]

**Category**: PLAN
**Decision Type**: [Architecture / Approach / Selection]

### Objective
[A design document that enables implementation without further major decisions]

### Success Criteria
- [ ] Selected approach has documented trade-off analysis
- [ ] All major components identified with interfaces defined
- [ ] Risks listed with mitigation strategies
- [ ] Decision log captures why alternatives were rejected
- [ ] Implementation order is specified with dependencies

### Constraints
- MUST NOT: depend on unvalidated assumptions (flag them explicitly)
- MUST: include at least one alternative considered and rejected
- MUST: identify the riskiest assumption and how to validate it first

### Output
[Format: ADR, design doc, or implementation roadmap]
```

---

## BUILD - Development {#build}

**Purpose**: Produce artifacts - code, content, documents, configurations.

**Structural Characteristics**:
- Input: Design document or clear specification
- Output: Working artifact with evidence of correctness
- Success criteria: Functionality, quality gates passed, format compliance
- Key constraint: MUST NOT sacrifice safety for speed

**Template**:
```markdown
## /goal: [Name]

**Category**: BUILD
**Language/Stack**: [Specific tech or format]
**Existing Context**: [What already exists]

### Objective
[Observable artifact with behavioral evidence of correctness]

### Success Criteria
- [ ] [Functional requirement with testable condition]
- [ ] [Functional requirement with testable condition]
- [ ] [Quality gate: lint, type check, format]
- [ ] [Edge case handled]
- [ ] [Error state handled]

### Constraints
- MUST NOT: [forbidden pattern]
- MUST: [required pattern]
- LIMIT: [quantified bound]

### Output
[Files, paths, format, naming]

### Verification Method
[Commands to run, expected outputs, manual checks]
```

---

## VERIFY - Testing {#verify}

**Purpose**: Check correctness, validate quality, confirm requirements met.

**Structural Characteristics**:
- Input: Artifact to verify + criteria to verify against
- Output: Structured review with per-criterion scoring
- Success criteria: Thoroughness, accuracy of findings, actionability of feedback
- Key constraint: MUST NOT approve with unaddressed Critical findings

**Template**:
```markdown
## /goal: [Name]

**Category**: VERIFY
**Subject**: [What is being verified]
**Reviewer Stance**: [Critical / Supportive / Neutral]

### Objective
[A structured assessment with clear pass/fail verdict per criterion]

### Review Criteria (rubric)
| Dimension | Weight | Pass Threshold |
|-----------|--------|----------------|
| [Dimension] | [%] | [Specific threshold] |

### Constraints
- MUST NOT: approve if any Critical finding is unaddressed
- MUST: provide specific line/section references for every issue
- MUST: distinguish Critical / Warning / Info severity

### Output
[Review format with sections and scoring]

### Verification Method
[How a third party confirms review quality]
```

---

## DEPLOY - Release {#deploy}

**Purpose**: Ship to production, make available, activate.

**Structural Characteristics**:
- Input: Verified artifact + deployment configuration
- Output: Live system with confirmation of health
- Success criteria: Zero-downtime, rollback verified, health checks pass
- Key constraint: MUST NOT deploy without verified rollback plan

**Template**:
```markdown
## /goal: [Name]

**Category**: DEPLOY
**Environment**: [Staging / Production / Canary]
**Rollback SLA**: [Maximum acceptable rollback time]

### Objective
[Observable: system live, healthy, serving traffic with verified rollback]

### Success Criteria
- [ ] Deployment completes without error
- [ ] Health checks pass for 5 consecutive minutes
- [ ] Rollback tested and completes within [SLA]
- [ ] Monitoring alerts configured for new functionality
- [ ] No increase in error rate > 0.1%

### Constraints
- MUST NOT: deploy without verified rollback procedure
- MUST NOT: skip health check verification
- MUST: have human approval gate for production deploy
- LIMIT: canary traffic ≤ 5% for first 15 minutes

### Verification Method
[Health check commands, monitoring dashboards, error rate queries]
```

---

## MONITOR - Operations {#monitor}

**Purpose**: Track health, detect issues, optimize performance.

**Structural Characteristics**:
- Input: Live system with monitoring infrastructure
- Output: Metrics, alerts, optimization recommendations
- Success criteria: Coverage, signal-to-noise ratio, response time
- Key constraint: MUST NOT miss critical failures (false negatives)

**Template**:
```markdown
## /goal: [Name]

**Category**: MONITOR
**System**: [What is being monitored]
**Alert SLA**: [Maximum time to detect critical issue]

### Objective
[Observable: health status known, anomalies detected, trends visible]

### Success Criteria
- [ ] All critical metrics have alerts with thresholds
- [ ] Dashboard shows system health at a glance
- [ ] Alert false positive rate < 10%
- [ ] Runbook exists for every alert type
- [ ] Performance trends tracked week-over-week

### Constraints
- MUST NOT: alert on metrics without runbook
- MUST: page on-call for critical user-facing errors only
- MUST: suppress alerts during known maintenance windows
- LIMIT: P99 latency alert threshold ≤ 500ms

### Output
[Dashboard URL, alert configs, runbook links, trend report]
```

---

## REFLECT - Learning {#reflect}

**Purpose**: Analyze outcomes, capture lessons, improve future work.

**Structural Characteristics**:
- Input: Completed work + observed outcomes
- Output: Retrospective document with actionable improvements
- Success criteria: Honesty, specificity, actionability, follow-up tracking
- Key constraint: MUST NOT blame individuals - focus on system improvements

**Template**:
```markdown
## /goal: [Name]

**Category**: REFLECT
**Scope**: [What work is being reflected on]

### Objective
[A retrospective document that enables measurably better future execution]

### Success Criteria
- [ ] What went well: 3+ specific practices to continue
- [ ] What needs improvement: 3+ specific issues with root cause
- [ ] Action items: each has owner and due date
- [ ] Quantified where possible (timelines, error rates, satisfaction)
- [ ] No individual blame - system/process focus only

### Constraints
- MUST NOT: assign blame to individuals
- MUST: link every action item to a specific, measurable outcome
- MUST: schedule follow-up to verify action items completed

### Output
[Retrospective document format with action item tracking]
```

---

## Stage Transitions {#transitions}

Quality gates between stages prevent low-quality work from propagating.

| From → To | Gate Criteria | Required Evidence |
|-----------|---------------|-------------------|
| EXPLORE → DEFINE | Findings reviewed and accepted | Research document with citations |
| DEFINE → PLAN | Scope approved by stakeholders | Signed-off scope document |
| PLAN → BUILD | Design reviewed, risks accepted | Approved design doc/ADR |
| BUILD → VERIFY | All tests pass, code reviewed | Green CI, review approval |
| VERIFY → DEPLOY | All Critical findings resolved | Verification report, sign-off |
| DEPLOY → MONITOR | System healthy post-deploy | 24h of green metrics |
| MONITOR → REFLECT | Sufficient data collected | Metrics summary |
| REFLECT → EXPLORE | Action items incorporated | Updated templates/processes |
