---
name: "agile-product-owner"
description: Agile product ownership for backlog management and sprint execution. Covers user story writing, acceptance criteria, sprint planning, and velocity tracking. Use for writing user stories, creating acceptance criteria, planning sprints, estimating story points, breaking down epics, or prioritizing backlog.
triggers:
  - write user story
  - create acceptance criteria
  - plan sprint
  - estimate story points
  - break down epic
  - prioritize backlog
  - sprint planning
  - INVEST criteria
  - Given When Then
  - user story template
  - sprint capacity
  - velocity tracking
---

# Agile Product Owner

Backlog management and sprint execution toolkit for product owners, including user story generation, acceptance criteria patterns, sprint planning, and velocity tracking.

---

## User Story Generation Workflow

Create INVEST-compliant user stories from requirements:

1. Launch a **`ticket-drafter` agent** with the raw requirement, bug report, or stakeholder request. The agent returns a complete ticket draft (title, type, priority, description) and flags any missing information. Resolve gaps before proceeding.
2. Review the draft — confirm the persona, action, and benefit are clear
3. Write acceptance criteria using Given-When-Then
4. Estimate story points using Fibonacci scale
5. Validate against INVEST criteria
6. Add to backlog with priority
7. **Validation:** Story passes all INVEST criteria; acceptance criteria are testable

### User Story Template

```
As a [persona],
I want to [action/capability],
So that [benefit/value].
```

**Example:**
```
As a marketing manager,
I want to export campaign reports to PDF,
So that I can share results with stakeholders who don't have system access.
```

### Story Types

| Type | Template | Example |
|------|----------|---------|
| Feature | As a [persona], I want to [action] so that [benefit] | As a user, I want to filter search results so that I find items faster |
| Improvement | As a [persona], I need [capability] to [goal] | As a user, I need faster page loads to complete tasks without frustration |
| Bug Fix | As a [persona], I expect [behavior] when [condition] | As a user, I expect my cart to persist when I refresh the page |
| Enabler | As a developer, I need to [technical task] to enable [capability] | As a developer, I need to implement caching to enable instant search |

---

## Acceptance Criteria Patterns

### Given-When-Then Template

```
Given [precondition/context],
When [action/trigger],
Then [expected outcome].
```

**Examples:**
```
Given the user is logged in with valid credentials,
When they click the "Export" button,
Then a PDF download starts within 2 seconds.

Given the user has entered an invalid email format,
When they submit the registration form,
Then an inline error message displays "Please enter a valid email address."
```

### Reviewing Tech Lead's Acceptance Criteria

When the Tech Lead has written acceptance criteria on a `todo` ticket, launch a **`requirements-reviewer` agent** with the ticket's title, description, acceptance criteria, and the original intent from intake. The agent returns:
- Overall assessment (READY / NEEDS REVISION / BLOCKED)
- Specific issues per AC item
- Scope gaps and scope creep
- A ready-to-paste comment for the ticket

Post the agent's recommended comment on the ticket. Do not move the ticket — that is the Tech Lead's call.

### Acceptance Criteria Checklist

Each story should include criteria for:

| Category | Example |
|----------|---------|
| Happy Path | Given valid input, When submitted, Then success message displayed |
| Validation | Should reject input when required field is empty |
| Error Handling | Must show user-friendly message when API fails |
| Performance | Should complete operation within 2 seconds |
| Accessibility | Must be navigable via keyboard only |

### Minimum Criteria by Story Size

| Story Points | Minimum AC Count |
|--------------|------------------|
| 1-2 | 3-4 criteria |
| 3-5 | 4-6 criteria |
| 8 | 5-8 criteria |
| 13+ | Split the story |

---

## Epic Breakdown Workflow

Break epics into deliverable sprint-sized stories:

1. Define epic scope and success criteria
2. Identify all personas affected
3. List all capabilities needed per persona
4. Group capabilities into logical stories
5. Validate each story is ≤8 points
6. Identify dependencies between stories
7. Sequence stories for incremental delivery

### Splitting Techniques

| Technique | When to Use | Example |
|-----------|-------------|---------|
| By workflow step | Linear process | "Checkout" → "Add to cart" + "Enter payment" + "Confirm order" |
| By persona | Multiple user types | "Dashboard" → "Admin dashboard" + "User dashboard" |
| By data type | Multiple inputs | "Import" → "Import CSV" + "Import Excel" |
| By operation | CRUD functionality | "Manage users" → "Create" + "Edit" + "Delete" |
| Happy path first | Risk reduction | "Feature" → "Basic flow" + "Error handling" + "Edge cases" |

### Epic Example

```
Epic: User Dashboard (34 points total)
├── US-001: View key metrics (5 pts) - End User
├── US-002: Customize layout (5 pts) - Power User
├── US-003: Export data to CSV (3 pts) - End User
├── US-004: Share with team (5 pts) - End User
├── US-005: Set up alerts (5 pts) - Power User
├── US-006: Filter by date range (3 pts) - End User
├── US-007: Admin overview (5 pts) - Admin
└── US-008: Enable caching (3 pts) - Enabler
```

---

## Sprint Planning Workflow

1. Calculate team capacity (velocity × availability)
2. Review sprint goal with stakeholders
3. Select stories from prioritized backlog
4. Fill to 80-85% of capacity (committed)
5. Add stretch goals (10-15% additional)
6. Identify dependencies and risks

### Capacity Calculation

```
Sprint Capacity = Average Velocity × Availability Factor

Example:
Average Velocity: 30 points
Team availability: 90%
Adjusted Capacity: 27 points
Committed: 23 points (85%)
Stretch: 4 points (15%)
```

### Availability Factors

| Scenario | Factor |
|----------|--------|
| Full sprint, no PTO | 1.0 |
| One team member out 50% | 0.9 |
| Holiday during sprint | 0.8 |
| Multiple members out | 0.7 |

### Sprint Loading Template

```
Sprint Capacity: 27 points
Sprint Goal: [Clear, measurable objective]

COMMITTED (23 points):
[H] US-001: User dashboard (5 pts)
[H] US-002: Export feature (3 pts)
[H] US-003: Search filter (5 pts)
[M] US-004: Settings page (5 pts)
[M] US-005: Help tooltips (3 pts)
[L] US-006: Theme options (2 pts)

STRETCH (4 points):
[L] US-007: Sort options (2 pts)
[L] US-008: Print view (2 pts)
```

---

## Backlog Prioritization

### Priority Levels

| Priority | Definition | Sprint Target |
|----------|------------|---------------|
| Critical | Blocking users, security, data loss | Immediate |
| High | Core functionality, key user needs | This sprint |
| Medium | Improvements, enhancements | Next 2-3 sprints |
| Low | Nice-to-have, minor improvements | Backlog |

### Prioritization Factors

| Factor | Weight | Questions |
|--------|--------|-----------|
| Business Value | 40% | Revenue impact? User demand? Strategic alignment? |
| User Impact | 30% | How many users? How frequently used? |
| Risk/Dependencies | 15% | Technical risk? External dependencies? |
| Effort | 15% | Size? Complexity? Uncertainty? |

### INVEST Criteria Validation

| Criterion | Pass If... |
|-----------|------------|
| **I**ndependent | No blocking dependencies on uncommitted stories |
| **N**egotiable | Multiple implementation approaches possible |
| **V**aluable | Clear benefit in "so that" clause |
| **E**stimable | Understood well enough to size |
| **S**mall | ≤8 story points, completable in one sprint |
| **T**estable | Clear, verifiable acceptance criteria |

---

## Sprint Metrics

| Metric | Formula | Target |
|--------|---------|--------|
| Velocity | Points completed / sprint | Stable ±10% |
| Commitment Reliability | Completed / Committed | >85% |
| Scope Change | Points added or removed mid-sprint | <10% |
| Carryover | Points not completed | <15% |

### Definition of Done

Story is complete when:
- [ ] Code complete and peer reviewed
- [ ] Unit tests written and passing
- [ ] Acceptance criteria verified
- [ ] Documentation updated
- [ ] Deployed to staging environment
- [ ] Product Owner accepted
- [ ] No critical bugs remaining

---

## Tools

```bash
# Generate stories from sample epic
python scripts/user_story_generator.py

# Plan sprint with capacity
python scripts/user_story_generator.py sprint 30
```
