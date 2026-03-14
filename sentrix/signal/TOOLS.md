# signal — Tools Reference

## Skills

| Skill | Purpose |
|---|---|
| `paperclip` | Interact with Paperclip control plane: manage pipeline directives, route tickets, report to vigil, coordinate scout/cipher/lumen |
| `para-memory-files` | Manage PARA file-based memory: store routing decisions, queue states, pipeline health logs |

---

## MCP Tools

| Tool | Purpose |
|---|---|
| `muninn_remember` | Store routing decisions, dwell time violations, confidence escalation decisions, pipeline flow patterns |
| `muninn_recall` | Query prior routing decisions, dwell time history, confidence policies, pipeline throughput data |
| `muninn_guide` | Retrieve procedural context for routing a specific ticket type or resolving a pipeline stage bottleneck |

---

## Paperclip API Reference

All requests: `Authorization: Bearer $PAPERCLIP_API_KEY`, base URL `$PAPERCLIP_API_URL`.
Get lumen/scout/cipher/vigil agent IDs: `GET /api/companies/$PAPERCLIP_COMPANY_ID/agents`

| Action | Endpoint |
|---|---|
| View intelligence queue (unrouted) | `GET /api/companies/$PAPERCLIP_COMPANY_ID/issues?status=todo` |
| View specific issue | `GET /api/issues/{id}` |
| Route to Backlog (engineering) | `PATCH /api/issues/{id}` `{ "status": "backlog" }` |
| Route to Backlog (design — reassign to canvas) | `PATCH /api/issues/{id}` `{ "status": "backlog", "assigneeAgentId": "{canvas-agent-id}" }` |
| Archive ticket | `PATCH /api/issues/{id}` `{ "status": "cancelled" }` |
| Add routing decision comment | `POST /api/issues/{id}/comments` `{ "body": "SIGNAL ROUTING: [Backlog / Archived] — [rationale]" }` |
| Add pipeline directive comment | `POST /api/issues/{id}/comments` `{ "body": "SIGNAL DIRECTIVE to [agent]: [action required by time]" }` |
| Add confidence hold comment | `POST /api/issues/{id}/comments` `{ "body": "CONFIDENCE HOLD: Score [N] — Escalated to vigil. Awaiting routing decision." }` |
| Return incomplete to lumen | `PATCH /api/issues/{id}` `{ "assigneeAgentId": "{lumen-agent-id}" }` |
| Add incomplete return comment | `POST /api/issues/{id}/comments` `{ "body": "RETURNED TO LUMEN: Missing fields — [list missing fields]. Complete before resubmission." }` |

---

## Intelligence Queue Dwell Time Rule

| Threshold | Action |
|---|---|
| < 30 minutes | Normal — monitor |
| 30–60 minutes | Check pipeline stage; issue reminder directive if stage is stalled |
| 60–90 minutes | Issue priority directive to responsible stage agent |
| 90–120 minutes | Issue urgent directive; prepare vigil notification |
| > 2 hours | Dwell time violation — escalate to vigil immediately |

---

## Routing Rules

| Ticket Type | Destination |
|---|---|
| UI-relevant (dashboard, suggestion cards, alert formats, visualisations) | Design Queue |
| Logic/data (backend processing, data models, integrations, analytics) | Backlog |
| Confidence below 60 | Hold — escalate to vigil before routing |
| Incomplete lumen fields | Return to lumen for remediation |

---

## Role-Specific Rules

- **2-hour dwell limit is a hard rule.** signal monitors every ticket from entry to Intelligence Queue and escalates violations to vigil immediately.
- **Score 5 decisions are signal's responsibility.** cipher escalates score 5 events; signal decides route to lumen or archive, with documented rationale.
- **Confidence 60 is the routing floor.** Below 60 cannot route without vigil sign-off. signal escalates these immediately.
- **All 7 lumen fields are required.** Missing any field: return to lumen before routing, no exceptions.
- **No direct communication with devcodex or pixel.** Routing to Backlog or Design Queue is the handoff — signal does not engage with engineering or design directly.

---

## General Rules

- All agent names are lowercase in all communications and files.
- Chain of command is always respected:
  - apex → forge → nexus → devcodex / pixel / verdict
  - apex → forge → relay
  - apex → vigil → signal → scout / cipher / lumen
  - apex → canvas → flux / prism
- No agent skips a level in the chain of command without explicit apex authorisation.
- Every cycle ends with fact extraction via `muninn_remember` and pipeline state logged to `./memory/pipeline/YYYY-MM-DD.md`.
- Kanban columns in order: INTELLIGENCE QUEUE → DESIGN QUEUE → BACKLOG → TODO → READY → IN PROGRESS → IN REVIEW → QA → DEPLOY → DONE → BLOCKED → CANCELLED
