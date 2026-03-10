---
name: "product-manager-toolkit"
description: Comprehensive toolkit for product managers including RICE prioritization, customer interview analysis, PRD templates, discovery frameworks, and go-to-market strategies. Use for feature prioritization, user research synthesis, requirement documentation, and product strategy development.
---

# Product Manager Toolkit

Essential tools and frameworks for modern product management, from discovery to delivery.

---

## Core Workflows

### Feature Prioritization Process

```
Gather → Score → Analyze → Plan → Validate → Execute
```

Steps: Gather requests → Score with RICE → Analyze portfolio → Generate roadmap → Validate results → Execute and iterate.

### Customer Discovery Process

```
Plan → Recruit → Interview → Analyze → Synthesize → Validate
```

Recruit 5-8 participants per segment. Focus on past behavior not future intentions.

### PRD Development Process

```
Scope → Draft → Review → Refine → Approve → Track
```

| Template | Use Case | Timeline |
|----------|----------|----------|
| Standard PRD | Complex features, cross-team | 6-8 weeks |
| One-Page PRD | Simple features, single team | 2-4 weeks |
| Feature Brief | Exploration phase | 1 week |
| Agile Epic | Sprint-based delivery | Ongoing |

---

## Tools Reference

### RICE Prioritizer

Scores features by Reach × Impact × Confidence ÷ Effort.

**CSV Input Format:**
```csv
name,reach,impact,confidence,effort,description
User Dashboard Redesign,5000,high,high,l,Complete redesign
Mobile Push Notifications,10000,massive,medium,m,Add push support
Dark Mode,8000,medium,high,s,Dark theme option
```

**Commands:**
```bash
python scripts/rice_prioritizer.py sample
python scripts/rice_prioritizer.py features.csv
python scripts/rice_prioritizer.py features.csv --capacity 20
python scripts/rice_prioritizer.py features.csv --output json
python scripts/rice_prioritizer.py features.csv --output csv
```

### Customer Interview Analyzer

Extracts pain points, feature requests, jobs-to-be-done patterns, sentiment analysis, themes/quotes, and competitor mentions from interview transcripts.

```bash
python scripts/customer_interview_analyzer.py interview.txt
python scripts/customer_interview_analyzer.py interview.txt json
```

---

## PRD Best Practices

**Writing Great PRDs:**
- Start with the problem, not the solution
- Include clear success metrics upfront
- Explicitly state what's out of scope
- Use visuals; keep technical details in appendix
- Version control all changes

**Key Sections:**
1. Problem statement and user context
2. Success metrics (leading and lagging)
3. User stories / jobs to be done
4. Functional requirements
5. Out of scope
6. Open questions
7. Launch criteria

---

## Prioritization Frameworks

### RICE Scoring

| Factor | Description | Scale |
|--------|-------------|-------|
| Reach | Users affected per period | # of users |
| Impact | Effect on each user | massive/high/medium/low/minimal |
| Confidence | Certainty in estimates | high/medium/low |
| Effort | Person-months to ship | person-months |

### MoSCoW Method

- **Must have** — critical, launch blocker
- **Should have** — important but not critical
- **Could have** — nice to have
- **Won't have** — explicitly out of scope this cycle

### Kano Model

- **Basic needs** — expected, dissatisfying if absent
- **Performance** — more = better
- **Delighters** — unexpected, creates delight

---

## Common Pitfalls

| Pitfall | Description | Prevention |
|---------|-------------|------------|
| **Solution-First** | Jumping to features before understanding problems | Start every PRD with problem statement |
| **Analysis Paralysis** | Over-researching without shipping | Set time-boxes for research phases |
| **Feature Factory** | Shipping features without measuring impact | Define success metrics before building |
| **Ignoring Tech Debt** | Not allocating time for platform health | Reserve 20% capacity for maintenance |
| **Stakeholder Surprise** | Not communicating early and often | Weekly async updates, monthly demos |
| **Metric Theater** | Optimizing vanity metrics over real value | Tie metrics to user value delivered |

---

## Effective Prioritization

- Mix quick wins with strategic bets
- Consider opportunity cost of each item
- Account for dependencies
- Buffer 20% for unexpected work
- Revisit priorities quarterly

---

## Integration Points

| Category | Platforms |
|----------|-----------|
| **Analytics** | Amplitude, Mixpanel, Google Analytics |
| **Roadmapping** | ProductBoard, Aha!, Roadmunk |
| **Design** | Figma, Sketch, Miro |
| **Development** | Jira, Linear, GitHub, Asana |
| **Research** | Dovetail, UserVoice, Pendo, Maze |
| **Communication** | Slack, Notion, Confluence |

---

## Reference Documents

- `references/prd_templates.md` — PRD templates for different contexts
- `references/frameworks.md` — Detailed framework documentation (RICE, MoSCoW, Kano, JTBD, etc.)
