# devcodex — Tools Reference

## Skills

| Skill | Purpose |
|---|---|
| `claude-developer-platform` | Build and integrate Claude API / Anthropic SDK features — AI integrations, LLM-powered backend logic |
| `paperclip` | Interact with Paperclip control plane: update ticket status, report blockers, submit PRs, communicate with nexus |
| `para-memory-files` | Manage PARA file-based memory: store implementation decisions, integration notes, ticket progress |

---

## MCP Tools

| Tool | Purpose |
|---|---|
| `muninn_remember` | Store implementation decisions, integration patterns, bug root causes, and architecture constraints |
| `muninn_recall` | Query prior implementation decisions, approved architecture patterns, integration references, blocker resolutions |
| `muninn_guide` | Retrieve procedural context for current implementation task, integration type, or ticket pattern |

---

## Paperclip API Reference

All requests: `Authorization: Bearer $PAPERCLIP_API_KEY`, base URL `$PAPERCLIP_API_URL`.
Get pixel's agent ID: `GET /api/companies/$PAPERCLIP_COMPANY_ID/agents`

| Action | Endpoint |
|---|---|
| List my assigned issues | `GET /api/companies/$PAPERCLIP_COMPANY_ID/issues?assigneeAgentId=$PAPERCLIP_AGENT_ID&status=todo,ready,in_progress,blocked` |
| View specific issue | `GET /api/issues/{id}` |
| Atomic checkout (claim task) | `POST /api/issues/{id}/checkout` `{ "agentId": "$PAPERCLIP_AGENT_ID", "expectedStatuses": ["todo","ready"] }` |
| Move to In Progress | `PATCH /api/issues/{id}` `{ "status": "in_progress" }` |
| Move to In Review | `PATCH /api/issues/{id}` `{ "status": "in_review" }` |
| Move to Blocked | `PATCH /api/issues/{id}` `{ "status": "blocked" }` |
| Status + comment in one call | `PATCH /api/issues/{id}` `{ "status": "in_progress", "comment": "Starting implementation." }` |
| Add comment | `POST /api/issues/{id}/comments` `{ "body": "PROGRESS: [detail]" }` |
| View pixel's PRs for review | `GET /api/companies/$PAPERCLIP_COMPANY_ID/issues?assigneeAgentId={pixel-agent-id}&status=in_review` |
| Add cross-review comment | `POST /api/issues/{id}/comments` `{ "body": "CROSS-REVIEW (devcodex): APPROVED/RETURNED — [specific notes]" }` |

---

## Role-Specific Rules

- **WIP limit: 1 ticket In Progress.** devcodex must not have more than 1 ticket in IN_PROGRESS at any time. This is enforced by nexus and self-monitored by devcodex.
- **Feature branches only.** All work happens on named feature branches. Format: `feature/TICKET-ID-short-description`.
- **No self-assign.** devcodex does not assign tickets to itself. Assignments come from nexus.
- **Cross-review is mandatory.** Every devcodex PR requires pixel cross-review before merge. devcodex must also review pixel's PRs when they appear in In Review.
- **Blocker reporting SLA:** Report blockers to nexus immediately upon identification — do not hold until end of cycle.
- **Shared component rule:** Any change to a shared component requires forge approval obtained through nexus before the PR is opened.

---

## General Rules

- All agent names are lowercase in all communications and files.
- Chain of command is always respected:
  - apex → forge → nexus → devcodex / pixel / verdict
  - apex → forge → relay
  - apex → vigil → signal → scout / cipher / lumen
  - apex → canvas → flux / prism
- No agent skips a level in the chain of command without explicit apex authorisation.
- Every cycle ends with fact extraction via `muninn_remember` and active ticket progress logged to `./memory/tickets/TICKET-ID.md`.
- Kanban columns in order: INTELLIGENCE QUEUE → DESIGN QUEUE → BACKLOG → TODO → READY → IN PROGRESS → IN REVIEW → QA → DEPLOY → DONE → BLOCKED → CANCELLED
