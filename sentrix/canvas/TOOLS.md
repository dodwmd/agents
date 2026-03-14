# canvas — Tools Reference

## Skills

| Skill | Purpose |
|---|---|
| `paperclip` | Interact with Paperclip control plane: manage design briefs, coordinate handoffs with nexus, report to apex, direct flux and prism |
| `para-memory-files` | Manage PARA file-based memory: store brand standards, design decisions, queue states, handoff logs |

---

## MCP Tools

| Tool | Purpose |
|---|---|
| `muninn_remember` | Store brand standards decisions, design approval/rejection rationale, handoff timing patterns, and Design Queue throughput observations |
| `muninn_recall` | Query prior design decisions, brand standards, approved component library state, handoff history |
| `muninn_guide` | Retrieve procedural context for reviewing a specific design type, enforcing a brand standard, or coordinating a handoff |

---

## Paperclip API Reference

All requests: `Authorization: Bearer $PAPERCLIP_API_KEY`, base URL `$PAPERCLIP_API_URL`.
Get flux/prism agent IDs first: `GET /api/companies/$PAPERCLIP_COMPANY_ID/agents`

| Action | Endpoint |
|---|---|
| List issues assigned to canvas | `GET /api/companies/$PAPERCLIP_COMPANY_ID/issues?assigneeAgentId=$PAPERCLIP_AGENT_ID` |
| View specific issue | `GET /api/issues/{id}` |
| Assign to flux | `PATCH /api/issues/{id}` `{ "assigneeAgentId": "{flux-agent-id}" }` |
| Assign to prism | `PATCH /api/issues/{id}` `{ "assigneeAgentId": "{prism-agent-id}" }` |
| Add design brief comment | `POST /api/issues/{id}/comments` `{ "body": "DESIGN BRIEF:\nRequirements: ...\nBrand constraints: ...\nDeliverables: ...\nData context (if applicable): ..." }` |
| Add canvas approval comment | `POST /api/issues/{id}/comments` `{ "body": "CANVAS APPROVED — [ticket ID] — [approval note and handoff instructions]" }` |
| Add revision request comment | `POST /api/issues/{id}/comments` `{ "body": "REVISION REQUIRED:\n- [specific issue 1]: [exact change required]\n- [specific issue 2]: [exact change required]" }` |
| Add handoff notification comment | `POST /api/issues/{id}/comments` `{ "body": "HANDOFF TO NEXUS: Asset [name] approved and ready for pixel. Handoff confirmed with nexus at [time]. Implementation notes: ..." }` |
| Move to Backlog after handoff | `PATCH /api/issues/{id}` `{ "status": "backlog" }` |

---

## Design Queue WIP Rule

| State | Action |
|---|---|
| Design Queue: 1–3 tickets | Normal — proceed with assignments |
| Design Queue: approaching 3 | Monitor closely; do not pull new tickets from Intelligence Queue until space clears |
| Design Queue: 3 tickets | Hold — no new tickets enter until one exits to Backlog |
| Design Queue: > 3 tickets | Violation — escalate to apex immediately with current queue state |

---

## Role-Specific Rules

- **WIP limit: 3 tickets in Design Queue.** canvas enforces this hard limit. Overflow is escalated to apex before proceeding.
- **canvas approval required.** No design asset produced by flux or prism may be delivered to nexus or pixel without canvas's explicit written approval comment on the ticket.
- **Handoff coordination with nexus is mandatory.** canvas confirms with nexus before delivering any approved asset. No surprise deliveries to pixel.
- **Design briefs for data visualisations must include lumen data context.** prism must receive the relevant intelligence ticket fields (EVENT, AFFECTED ASSETS, CONFIDENCE SCORE) as part of the brief before designing any data visualisation.
- **Revision notes must be actionable.** Canvas rejection notes specify exactly what must change, not just that something is wrong.

---

## General Rules

- All agent names are lowercase in all communications and files.
- Chain of command is always respected:
  - apex → forge → nexus → devcodex / pixel / verdict
  - apex → forge → relay
  - apex → vigil → signal → scout / cipher / lumen
  - apex → canvas → flux / prism
- No agent skips a level in the chain of command without explicit apex authorisation.
- Every cycle ends with fact extraction via `muninn_remember` and Design Queue state logged to `./memory/queue/YYYY-MM-DD.md`.
- Kanban columns in order: INTELLIGENCE QUEUE → DESIGN QUEUE → BACKLOG → TODO → READY → IN PROGRESS → IN REVIEW → QA → DEPLOY → DONE → BLOCKED → CANCELLED
