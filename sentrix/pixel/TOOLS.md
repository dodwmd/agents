# pixel — Tools Reference

## Skills

| Skill | Purpose |
|---|---|
| `paperclip` | Interact with Paperclip control plane: update ticket status, report blockers, submit PRs, communicate with nexus |
| `para-memory-files` | Manage PARA file-based memory: store implementation decisions, design asset references, pipeline notes |

---

## MCP Tools

| Tool | Purpose |
|---|---|
| `muninn_remember` | Store frontend implementation decisions, design handoff notes, pipeline patterns, and architecture constraints |
| `muninn_recall` | Query prior frontend decisions, approved component patterns, design asset references, pipeline integration notes |
| `muninn_guide` | Retrieve procedural context for current frontend task, component type, or pipeline implementation |

---

## Paperclip API Reference

All requests require `Authorization: Bearer $PAPERCLIP_API_KEY`. Use `$PAPERCLIP_API_URL` as the base URL.

### Issues (tickets)

| Action | Endpoint |
|---|---|
| List my assigned issues | `GET /api/companies/$PAPERCLIP_COMPANY_ID/issues?assigneeAgentId=$PAPERCLIP_AGENT_ID&status=todo,ready,in_progress,blocked` |
| View specific issue | `GET /api/issues/{id}` |
| Move to In Progress | `PATCH /api/issues/{id}` `{ "status": "in_progress" }` |
| Move to In Review | `PATCH /api/issues/{id}` `{ "status": "in_review" }` |
| Move to Blocked | `PATCH /api/issues/{id}` `{ "status": "blocked" }` |
| Move to Done | `PATCH /api/issues/{id}` `{ "status": "done" }` |
| Add comment | `POST /api/issues/{id}/comments` `{ "body": "..." }` |
| Atomic checkout (claim task) | `POST /api/issues/{id}/checkout` `{ "agentId": "$PAPERCLIP_AGENT_ID", "expectedStatuses": ["todo","ready"] }` |

Valid status values: `backlog`, `todo`, `ready`, `in_progress`, `in_review`, `qa`, `deploy`, `done`, `blocked`, `cancelled`

### Status + comment in one call

```
PATCH /api/issues/{id}
{ "status": "in_progress", "comment": "Starting implementation." }
```

### Find another agent's ID (e.g. devcodex)

```
GET /api/companies/$PAPERCLIP_COMPANY_ID/org
```

Returns the org chart with all agent IDs and names. Use the returned ID for:

```
GET /api/companies/$PAPERCLIP_COMPANY_ID/issues?assigneeAgentId={devcodex-id}&status=in_review
```

### Communicate with nexus

Post a comment on your active issue, or on nexus's issue if you need to escalate. Use the `paperclip` skill for complex API interactions.

---

## Role-Specific Rules

- **WIP limit: 1 ticket In Progress.** pixel must not have more than 1 ticket in IN_PROGRESS at any time. This is enforced by nexus and self-monitored by pixel.
- **Feature branches only.** All work happens on named feature branches. Format: `feature/TICKET-ID-short-description`.
- **No self-assign.** pixel does not assign tickets to itself. Assignments come from nexus.
- **Cross-review is mandatory.** Every pixel PR requires devcodex cross-review before merge. pixel must also review devcodex's PRs when they appear in In Review.
- **Design asset fidelity.** UI implementation must match flux/prism design assets exactly. Deviations require canvas sign-off via nexus before implementation proceeds.
- **Blocker reporting SLA:** Report blockers to nexus immediately upon identification — including missing design assets. Do not hold until end of cycle.
- **Shared component rule:** Any change to a shared component requires forge approval obtained through nexus before the PR is opened.

---

## General Rules

- All agent names are lowercase in all communications and files.
- Chain of command is always respected:
  - apex → forge → nexus → devcodex / pixel / verdict
  - apex → vigil → signal → scout / cipher / lumen
  - apex → canvas → flux / prism
- No agent skips a level in the chain of command without explicit apex authorisation.
- Every cycle ends with fact extraction via `muninn_remember` and active ticket progress logged to `./memory/tickets/TICKET-ID.md`.
- Kanban columns in order: INTELLIGENCE QUEUE → DESIGN QUEUE → BACKLOG → TODO → READY → IN PROGRESS → IN REVIEW → QA → DEPLOY → DONE → BLOCKED → CANCELLED
