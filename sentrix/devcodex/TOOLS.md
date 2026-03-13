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

## Kanban API Reference

| Action | Endpoint |
|---|---|
| View my assigned tickets | `GET /kanban/tickets?assignee=devcodex` |
| View specific ticket | `GET /kanban/tickets/{id}` |
| Move ticket to In Progress | `PATCH /kanban/tickets/{id}` `{ "column": "IN_PROGRESS" }` |
| Move ticket to In Review | `PATCH /kanban/tickets/{id}` `{ "column": "IN_REVIEW" }` |
| Move ticket to Blocked | `PATCH /kanban/tickets/{id}` `{ "column": "BLOCKED" }` |
| Add progress comment | `POST /kanban/tickets/{id}/comments` `{ "body": "PROGRESS: [detail]" }` |
| Add PR submission comment | `POST /kanban/tickets/{id}/comments` `{ "body": "PR SUBMITTED: [branch] — [PR link]\nCriteria covered: ..." }` |
| Add blocker comment | `POST /kanban/tickets/{id}/comments` `{ "body": "BLOCKED: [precise description]\nAttempted: [what was tried]\nNeeded: [what would unblock]" }` |
| View pixel's PRs for review | `GET /kanban/tickets?column=IN_REVIEW&assignee=pixel` |
| Add cross-review comment | `POST /kanban/tickets/{id}/comments` `{ "body": "CROSS-REVIEW (devcodex): APPROVED/RETURNED — [specific notes]" }` |

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
  - apex → vigil → signal → scout / cipher / lumen
  - apex → canvas → flux / prism
- No agent skips a level in the chain of command without explicit apex authorisation.
- Every cycle ends with fact extraction via `muninn_remember` and active ticket progress logged to `./memory/tickets/TICKET-ID.md`.
- Kanban columns in order: INTELLIGENCE QUEUE → DESIGN QUEUE → BACKLOG → TODO → READY → IN PROGRESS → IN REVIEW → QA → DEPLOY → DONE → BLOCKED → CANCELLED
