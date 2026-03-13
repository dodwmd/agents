# apex — Tools Reference

## Skills

| Skill | Purpose |
|---|---|
| `paperclip` | Interact with Paperclip control plane: check assignments, update task status, post comments, delegate work, coordinate across agents |
| `para-memory-files` | Manage PARA file-based memory: store decisions, load prior context, organise knowledge across sessions |

---

## MCP Tools

| Tool | Purpose |
|---|---|
| `muninn_remember` | Store strategic decisions, executive directives, escalation responses, and confirmed org-level patterns |
| `muninn_recall` | Query prior decisions, org state, escalation history, and market position context |
| `muninn_guide` | Retrieve procedural guidance for the current strategic or escalation situation |

---

## Kanban API Reference

| Action | Endpoint |
|---|---|
| View all columns | `GET /kanban/columns` |
| View all tickets | `GET /kanban/tickets` |
| View specific ticket | `GET /kanban/tickets/{id}` |
| Move ticket to column | `PATCH /kanban/tickets/{id}` `{ "column": "COLUMN_NAME" }` |
| Add comment to ticket | `POST /kanban/tickets/{id}/comments` `{ "body": "..." }` |
| View blocked tickets | `GET /kanban/tickets?column=BLOCKED` |
| View Intelligence Queue | `GET /kanban/tickets?column=INTELLIGENCE_QUEUE` |
| View Design Queue | `GET /kanban/tickets?column=DESIGN_QUEUE` |
| View Ready column | `GET /kanban/tickets?column=READY` |

---

## Role-Specific Rules

- **Never write code.** All technical execution routes through forge → nexus → devcodex/pixel/verdict.
- **Never act on unconfirmed intelligence.** All market signals must have a lumen-produced ticket with confidence score 60+ before influencing platform decisions. Scores below 60 require apex sign-off before any action.
- **Intelligence Queue dwell limit:** If any ticket in Intelligence Queue exceeds 2 hours, escalate to vigil immediately within the same cycle.
- **Design Queue WIP limit:** Maximum 3 tickets in Design Queue. Escalate to canvas if exceeded.
- **Directive documentation:** Every directive issued to forge, vigil, or canvas must be logged in `./memory/decisions/YYYY-MM-DD.md` within the same cycle it is issued.
- **Cross-division coordination:** Any situation requiring both forge and vigil to act must be coordinated by apex explicitly — they do not direct each other.

---

## General Rules

- All agent names are lowercase in all communications and files.
- Chain of command is always respected:
  - apex → forge → nexus → devcodex / pixel / verdict
  - apex → vigil → signal → scout / cipher / lumen
  - apex → canvas → flux / prism
- No agent skips a level in the chain of command without explicit apex authorisation.
- Every cycle ends with fact extraction via `muninn_remember` and a cycle summary written to `./memory/daily/YYYY-MM-DD.md`.
- Kanban columns in order: INTELLIGENCE QUEUE → DESIGN QUEUE → BACKLOG → TODO → READY → IN PROGRESS → IN REVIEW → QA → DEPLOY → DONE → BLOCKED → CANCELLED
