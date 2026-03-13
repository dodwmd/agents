# cipher — Tools Reference

## Skills

| Skill | Purpose |
|---|---|
| `paperclip` | Interact with Paperclip control plane: receive flags from scout, route scored tickets, escalate to signal |
| `para-memory-files` | Manage PARA file-based memory: store scoring logs, calibration notes, escalation history |

---

## MCP Tools

| Tool | Purpose |
|---|---|
| `muninn_remember` | Store scoring decisions and rationales, calibration observations, event category patterns |
| `muninn_recall` | Query prior scoring decisions for similar events, scoring criteria, calibration history |
| `muninn_guide` | Retrieve procedural context for scoring a specific event type or ambiguous flag |

---

## Kanban API Reference

| Action | Endpoint |
|---|---|
| View cipher's scoring queue | `GET /kanban/tickets?column=INTELLIGENCE_QUEUE&assignee=cipher` |
| View specific ticket | `GET /kanban/tickets/{id}` |
| Add score output comment | `POST /kanban/tickets/{id}/comments` `{ "body": "SCORE: [N]\nCATEGORY: [category]\nRATIONALE: [one sentence]" }` |
| Forward to lumen (score 6+) | `PATCH /kanban/tickets/{id}` `{ "assignee": "lumen" }` |
| Escalate score 5 to signal | `POST /kanban/tickets/{id}/comments` `{ "body": "CIPHER ESCALATION — Score 5: [score output + flag summary]. Awaiting signal routing decision." }` |
| Reassign score 5 to signal | `PATCH /kanban/tickets/{id}` `{ "assignee": "signal" }` |
| Archive score 1–4 | `PATCH /kanban/tickets/{id}` `{ "column": "CANCELLED" }` |
| Add archive reason comment | `POST /kanban/tickets/{id}/comments` `{ "body": "ARCHIVED — Score [N]. Category: [category]. Rationale: [one sentence]. Below threshold." }` |
| Return incomplete flag to scout | `POST /kanban/tickets/{id}/comments` `{ "body": "RETURNED TO SCOUT: Missing required fields — [list]. Resubmit with all 4 fields: SOURCE, TIMESTAMP, HEADLINE, DESCRIPTION." }` |
| Reassign returned flag to scout | `PATCH /kanban/tickets/{id}` `{ "assignee": "scout" }` |

---

## Scoring Criteria Table

| Event Category | Score Range | Key Drivers |
|---|---|---|
| Central bank decisions, statements, or surprises | 8–10 | Interest rates, currency, equity valuations — global scope |
| Geopolitical events (conflicts, sanctions, major crises) | 8–10 | Broad market disruption; supply chain, commodity, currency effects |
| Regulatory announcements (crypto / finance) | 7–9 | Direct rule-change impact on asset classes, market access, business models |
| Major earnings surprises or significant M&A | 6–8 | Sector and related-asset repricing; potential cascade to broader market |
| Natural disasters (supply chain / economic impact) | 6–8 | Commodity, sector, and regional market disruption |
| Social sentiment spikes (coordinated / viral) | 5–7 | Short-term volatility potential; signal reliability varies |
| Major economy elections | 6–9 | Policy uncertainty; range depends on proximity and polling divergence |
| Unrelated news (entertainment, sports, non-financial) | 1–3 | Minimal to no direct market relevance |

## Score Placement Within Range

| Factor | Higher in Range | Lower in Range |
|---|---|---|
| Event certainty | Confirmed | Unverified rumour |
| Geographic scope | Global / multi-market | Local / single sector |
| Impact proximity | Direct effect on major asset class | Indirect / second-order |
| Event magnitude | Major policy shift | Routine decision |

---

## Scoring Output Format (Required)

Every scored flag must have this comment attached before routing:

```
SCORE: [1–10]
CATEGORY: [Event category from scoring table]
RATIONALE: [One sentence — what drove the score placement]
```

---

## Role-Specific Rules

- **Rationale is mandatory.** No score is submitted without a one-line rationale. No exceptions.
- **Score threshold routing:**
  - Score 6–10 → forward to lumen (reassign ticket to lumen)
  - Score 5 → escalate to signal (reassign to signal with CIPHER ESCALATION comment)
  - Score 1–4 → archive (move to CANCELLED with archive comment)
- **Score 5 never archived independently.** cipher does not archive a score 5 without signal's decision.
- **Incomplete flags returned.** Flags missing any of the 4 required fields (SOURCE, TIMESTAMP, HEADLINE, DESCRIPTION) are returned to scout before scoring.
- **No market direction assessment.** Rationale explains the score, not the direction markets will move.

---

## General Rules

- All agent names are lowercase in all communications and files.
- Chain of command is always respected:
  - apex → forge → nexus → devcodex / pixel / verdict
  - apex → vigil → signal → scout / cipher / lumen
  - apex → canvas → flux / prism
- No agent skips a level in the chain of command without explicit apex authorisation.
- Every cycle ends with fact extraction via `muninn_remember` and all scoring decisions logged to `./memory/scores/YYYY-MM-DD.md`.
- Kanban columns in order: INTELLIGENCE QUEUE → DESIGN QUEUE → BACKLOG → TODO → READY → IN PROGRESS → IN REVIEW → QA → DEPLOY → DONE → BLOCKED → CANCELLED
