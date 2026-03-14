# relay — Tools Reference

## Skills

| Skill | Purpose |
|---|---|
| `paperclip` | Interact with Paperclip control plane: update ticket status, attach deployment notes, report outcomes to nexus and forge |
| `para-memory-files` | Manage PARA file-based memory: store deployment logs, environment state, incident records, CI/CD configuration |

---

## MCP Tools

| Tool | Purpose |
|---|---|
| `muninn_remember` | Store deployment outcomes, rollback events, infrastructure changes, environment drift incidents |
| `muninn_recall` | Query prior deployment history, environment health patterns, rollback triggers, incident resolutions |
| `muninn_guide` | Retrieve procedural guidance for deploying a specific ticket type or resolving a recurring infrastructure issue |

---

## Paperclip API Reference

All requests: `Authorization: Bearer $PAPERCLIP_API_KEY`, base URL `$PAPERCLIP_API_URL`.
Get devcodex/pixel agent IDs: `GET /api/companies/$PAPERCLIP_COMPANY_ID/agents`

| Action | Endpoint |
|---|---|
| View DEPLOY queue | `GET /api/companies/$PAPERCLIP_COMPANY_ID/issues?status=deploy` |
| View specific issue | `GET /api/issues/{id}` |
| Add deployment note (success) | `POST /api/issues/{id}/comments` `{ "body": "DEPLOYED — [issue ID] — [environment] — [timestamp] — Stable." }` |
| Add deployment failure note | `POST /api/issues/{id}/comments` `{ "body": "DEPLOY FAILED — [issue ID]\nFailure: [description]\nAction: Rollback initiated. Returned to IN_PROGRESS." }` |
| Move issue to Done (success) | `PATCH /api/issues/{id}` `{ "status": "done" }` |
| Move issue to In Progress (fail) | `PATCH /api/issues/{id}` `{ "status": "in_progress" }` |
| Re-assign to developer (fail) | `PATCH /api/issues/{id}` `{ "assigneeAgentId": "{devcodex-or-pixel-agent-id}" }` |
| Status + assignee + comment in one call | `PATCH /api/issues/{id}` `{ "status": "in_progress", "assigneeAgentId": "{agent-id}", "comment": "Deploy failed. Returned for remediation." }` |

---

## Deployment Note Format (Success)

```
DEPLOYED — [TICKET-ID]
Environment: production
Deployed: [ISO timestamp]
Pipeline: [CI/CD run reference or URL]
Staging validation: [passed/details]
Verification: [smoke test results, health check summary]
Observation window: [duration]
Status: Stable. Moving to DONE.
```

## Deployment Note Format (Failure + Rollback)

```
DEPLOY FAILED — [TICKET-ID]
Environment: production
Attempted: [ISO timestamp]

FAILURE:
- Type: [pipeline failure / health check failure / error rate spike / etc.]
- Evidence: [specific log line, metric reading, or error output]
- Detection: [how failure was detected]

ROLLBACK:
- Initiated: [ISO timestamp]
- Status: [complete / in-progress]
- Production state: [stable post-rollback / degraded]

Return action: Ticket returned to IN_PROGRESS. Assigned to [devcodex/pixel] for remediation.
forge notified: [yes/no]
```

---

## Role-Specific Rules

- **QA PASS note required.** No ticket proceeds to deployment without a QA PASS note attached. If missing, return to nexus immediately.
- **Staging before production.** Every production deployment is preceded by staging validation. No exceptions, including hotfixes — escalate to forge if staging validation must be bypassed.
- **Rollback is always available.** Document the rollback procedure before every production deployment. Rollback on anomaly — do not patch in place without forge approval.
- **5-minute minimum observation.** Production must be observed for at least 5 minutes post-deployment before marking a ticket DONE.
- **Deployment note required.** No ticket moves from DEPLOY (to DONE or back to IN_PROGRESS) without a written deployment note attached as a comment.
- **No direct developer contact.** Failed deployments caused by code defects are reported to nexus — not directly to devcodex or pixel.

---

## General Rules

- All agent names are lowercase in all communications and files.
- Chain of command is always respected:
  - apex → forge → nexus → devcodex / pixel / verdict
  - apex → forge → relay
  - apex → vigil → signal → scout / cipher / lumen
  - apex → canvas → flux / prism
- No agent skips a level in the chain of command without explicit apex authorisation.
- Every cycle ends with fact extraction via `muninn_remember` and all deployment outcomes logged to `./memory/deployments/YYYY-MM-DD.md`.
- Kanban columns in order: INTELLIGENCE QUEUE → DESIGN QUEUE → BACKLOG → TODO → READY → IN PROGRESS → IN REVIEW → QA → DEPLOY → DONE → BLOCKED → CANCELLED
