---
description: Memory operations reference. Defines when to use muninndb MCP tools vs PARA files, and how to store and retrieve each type of information correctly.
---

# Memory Reference

Two layers. Each has a job. Don't mix them.

---

## Layer 1: muninndb — Episodic and Semantic Memory

Use for anything you'd retrieve by asking **"what do I know about X?"**

### When to store (`muninn_remember`)

Store **immediately** when any of these surface — do not batch:

- A new fact about a person (role, preference, relationship, history)
- A new fact about a company (stage, product, team size, competitors)
- A decision made and its rationale ("chose X over Y because...")
- A pattern observed ("customer objections cluster around pricing, not features")
- A strategic insight worth keeping ("the north star metric is activation, not signups")
- A constraint discovered ("engineering can't take on new work until Q2")
- An opinion or preference expressed by the board or a stakeholder

**Format**: Be specific. Bad: "meeting went well." Good: "Board approved $50K budget for Q2 hiring; constraint is headcount must be remote-only."

### When to retrieve (`muninn_recall`)

Call `muninn_recall` before:

- Starting a strategy session, board meeting, or war room (pull context on the topic)
- Engaging with a known entity — person, company, or competitor (pull what you know about them)
- Answering a question that prior context might inform
- Starting a new heartbeat (pull today's date + wake topic to surface anything relevant)

muninndb handles decay and strengthening automatically — memories recalled frequently stay strong; unused ones fade. This means you should query often, not just when you're sure you stored something.

---

## Layer 2: PARA Files — Structured Operational Memory

Use for structured artifacts that are **explicitly loaded at the start of a workflow**, not queried.

### What goes in files

| Artifact | Path | Loaded by |
|---|---|---|
| Today's plan and progress | `$AGENT_HOME/memory/YYYY-MM-DD.md` | Heartbeat step 2 |
| Approved board decisions | `$AGENT_HOME/memory/board-meetings/decisions.md` | `/board` Phase 1 |
| Raw board transcripts | `$AGENT_HOME/memory/board-meetings/YYYY-MM-DD-raw.md` | Never auto-loaded |
| Intel sweep results | `$AGENT_HOME/memory/intel/YYYY-MM-DD.md` | `/intel` Phase 1 |
| War room logs | `$AGENT_HOME/memory/YYYY-MM-DD.md` (war-room section) | On request |

### Format for daily notes (`YYYY-MM-DD.md`)

```markdown
## Today's Plan
- [ ] Task 1
- [ ] Task 2

## Timeline
- HH:MM — [what happened / what was decided / what was delegated]

## Blockers
- [anything unresolved]
```

### Format for decision log (`decisions.md`)

Append-only. Never delete entries.

```markdown
## YYYY-MM-DD — [Topic]
**Decision**: [one sentence]
**Rationale**: [why this option over others]
**Owner**: [agent or person responsible]
**Review date**: [when to revisit]
```

---

## Decision Table

| You want to... | Use |
|---|---|
| Store a fact about a person or company | `muninn_remember` |
| Store a strategic decision + rationale | `muninn_remember` + append to `decisions.md` |
| Recall what you know about a competitor | `muninn_recall` |
| Load today's task list | Read `$AGENT_HOME/memory/YYYY-MM-DD.md` |
| Load prior board decisions for a meeting | Read `$AGENT_HOME/memory/board-meetings/decisions.md` |
| Record what happened today | Write to `$AGENT_HOME/memory/YYYY-MM-DD.md` timeline |
| Find out what tools muninndb has | `muninn_guide` |
