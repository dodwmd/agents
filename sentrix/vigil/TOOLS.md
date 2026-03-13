# vigil — Tools Reference

## Skills

| Skill | Purpose |
|---|---|
| `paperclip` | Interact with Paperclip control plane: receive escalations from signal, deliver high-impact alerts to apex, post pipeline directives |
| `para-memory-files` | Manage PARA file-based memory: store escalations, load pipeline health, organise intelligence policy across sessions |

---

## MCP Tools

| Tool | Purpose |
|---|---|
| `muninn_remember` | Store escalation decisions, confidence policy overrides, pipeline quality patterns, and apex responses |
| `muninn_recall` | Query prior escalations, confidence threshold policies, pipeline health history, and confirmed market event records |
| `muninn_guide` | Retrieve procedural context for current pipeline escalation or routing decision |

---

## Kanban API Reference

| Action | Endpoint |
|---|---|
| View all columns | `GET /kanban/columns` |
| View Intelligence Queue | `GET /kanban/tickets?column=INTELLIGENCE_QUEUE` |
| View specific ticket | `GET /kanban/tickets/{id}` |
| Check ticket dwell time | `GET /kanban/tickets/{id}` → check `created_at` vs current time |
| Add pipeline directive comment | `POST /kanban/tickets/{id}/comments` `{ "body": "VIGIL: [directive]" }` |
| Move ticket to Backlog | `PATCH /kanban/tickets/{id}` `{ "column": "BACKLOG" }` |
| Move ticket to Design Queue | `PATCH /kanban/tickets/{id}` `{ "column": "DESIGN_QUEUE" }` |
| Archive ticket (Cancelled) | `PATCH /kanban/tickets/{id}` `{ "column": "CANCELLED" }` |

---

## lumen Structured Ticket Format (Required Fields)

Every intelligence ticket produced by lumen must contain all of the following fields. vigil validates these before any ticket is routed.

| Field | Description |
|---|---|
| `EVENT` | One-sentence description of the market-moving event |
| `SOURCE` | Primary source(s) used for confirmation |
| `AFFECTED ASSETS` | Asset classes, tickers, or sectors impacted |
| `IMPACT DIRECTION` | Bullish / Bearish / Neutral |
| `CONFIDENCE SCORE` | 1–100 numeric score |
| `RATIONALE` | 2–3 sentences explaining the confidence score and impact assessment |
| `RECOMMENDED PLATFORM ACTION` | Specific suggested action for the Sentrix platform |

---

## Role-Specific Rules

- **Intelligence Queue dwell limit:** 2 hours maximum. vigil must escalate to signal immediately if any ticket exceeds this.
- **Confidence gating:** Any ticket with a confidence score below 60 cannot be routed to Backlog or Design Queue without apex sign-off. vigil obtains that sign-off or archives the ticket.
- **High-impact escalation:** Events confirmed at confidence 80+ with major market impact must be escalated to apex immediately — not batched with cycle reporting.
- **lumen ticket validation:** vigil checks all 7 required fields before routing. Incomplete tickets are returned to signal for remediation.
- **No unconfirmed intelligence in engineering:** vigil is the last checkpoint before intelligence reaches the engineering pipeline. This gate never opens for unconfirmed or sub-60 confidence intelligence.

---

## General Rules

- All agent names are lowercase in all communications and files.
- Chain of command is always respected:
  - apex → forge → nexus → devcodex / pixel / verdict
  - apex → vigil → signal → scout / cipher / lumen
  - apex → canvas → flux / prism
- No agent skips a level in the chain of command without explicit apex authorisation.
- Every cycle ends with fact extraction via `muninn_remember` and pipeline health logged to `./memory/pipeline/YYYY-MM-DD.md`.
- Kanban columns in order: INTELLIGENCE QUEUE → DESIGN QUEUE → BACKLOG → TODO → READY → IN PROGRESS → IN REVIEW → QA → DEPLOY → DONE → BLOCKED → CANCELLED
