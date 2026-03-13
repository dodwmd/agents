# nexus — Tools Reference

## Skills

| Skill | Purpose |
|---|---|
| `paperclip` | Interact with Paperclip control plane: manage task assignments, post acceptance criteria, escalate blockers, deliver reports to forge |
| `para-memory-files` | Manage PARA file-based memory: store board snapshots, criteria patterns, blocker logs, and engineering reports |

---

## MCP Tools

| Tool | Purpose |
|---|---|
| `muninn_remember` | Store acceptance criteria patterns, blocker resolutions, developer velocity observations, and forge directive outcomes |
| `muninn_recall` | Query prior acceptance criteria templates, blocker resolution history, developer capacity state, ticket history |
| `muninn_guide` | Retrieve procedural guidance for writing criteria for a specific ticket type or resolving a known blocker pattern |

---

## Kanban API Reference

| Action | Endpoint |
|---|---|
| View all columns | `GET /kanban/columns` |
| View all tickets | `GET /kanban/tickets` |
| View specific column | `GET /kanban/tickets?column=COLUMN_NAME` |
| View specific ticket | `GET /kanban/tickets/{id}` |
| Create new ticket | `POST /kanban/tickets` `{ "title": "...", "description": "...", "column": "BACKLOG" }` |
| Move ticket to column | `PATCH /kanban/tickets/{id}` `{ "column": "COLUMN_NAME" }` |
| Assign ticket to developer | `PATCH /kanban/tickets/{id}` `{ "assignee": "devcodex" }` |
| Add acceptance criteria | `POST /kanban/tickets/{id}/comments` `{ "body": "ACCEPTANCE CRITERIA:\n- ...\n- ..." }` |
| Add blocker note | `POST /kanban/tickets/{id}/comments` `{ "body": "BLOCKED: [reason]\nResolution path: [action]" }` |
| Move to Ready | `PATCH /kanban/tickets/{id}` `{ "column": "READY" }` |
| Move to Blocked | `PATCH /kanban/tickets/{id}` `{ "column": "BLOCKED" }` |

---

## WIP Limits (nexus Enforces)

| Column | Limit | Applies To |
|---|---|---|
| In Progress | 1 per developer | devcodex, pixel independently |
| In Review | 4 total | All developers combined |
| Ready | 5–10 target | nexus maintains this range |
| Design Queue | 3 max | canvas enforces; nexus monitors for handoff coordination |

---

## Role-Specific Rules

- **Never write code.** nexus writes acceptance criteria, manages flow, and unblocks — never implementation.
- **Ready column target:** Maintain 5–10 tickets in Ready at all times. Below 5 = pull from TODO and write criteria immediately. Above 10 = hold until developers consume.
- **WIP enforcement:** devcodex and pixel each have a maximum of 1 ticket In Progress. nexus does not assign a second ticket until the first is complete or formally BLOCKED.
- **In Review limit:** Maximum 4 tickets in In Review at any time. If at limit, nexus instructs developers to prioritise reviews before starting new work.
- **Blocker SLA:** 2 hours from blocker raised to resolution or escalation to forge. nexus tracks blocker time from the moment it is flagged.
- **Shared component PRs:** nexus reviews for completeness, then escalates to forge for final approval. nexus does not merge shared component changes independently.
- **Cross-review mandatory:** devcodex reviews pixel's PRs; pixel reviews devcodex's PRs. nexus confirms this happened before approving at its level.
- **Acceptance criteria before Ready:** No ticket enters the Ready column without a written, complete acceptance criteria checklist.

---

## General Rules

- All agent names are lowercase in all communications and files.
- Chain of command is always respected:
  - apex → forge → nexus → devcodex / pixel / verdict
  - apex → vigil → signal → scout / cipher / lumen
  - apex → canvas → flux / prism
- No agent skips a level in the chain of command without explicit apex authorisation.
- Every cycle ends with fact extraction via `muninn_remember` and board state logged to `./memory/board/YYYY-MM-DD.md`.
- Kanban columns in order: INTELLIGENCE QUEUE → DESIGN QUEUE → BACKLOG → TODO → READY → IN PROGRESS → IN REVIEW → QA → DEPLOY → DONE → BLOCKED → CANCELLED
