# verdict — Tools Reference

## Skills

| Skill | Purpose |
|---|---|
| `paperclip` | Interact with Paperclip control plane: update ticket status, attach QA notes, report results to nexus |
| `para-memory-files` | Manage PARA file-based memory: store QA results, failure patterns, note templates |

---

## MCP Tools

| Tool | Purpose |
|---|---|
| `muninn_remember` | Store recurring failure patterns, acceptance criteria ambiguities, effective QA note structures |
| `muninn_recall` | Query prior QA results for similar ticket types, known failure patterns, test coverage approaches |
| `muninn_guide` | Retrieve procedural guidance for testing a specific feature type or acceptance criteria pattern |

---

## Kanban API Reference

| Action | Endpoint |
|---|---|
| View QA queue | `GET /kanban/tickets?column=QA` |
| View specific ticket | `GET /kanban/tickets/{id}` |
| Add QA pass note | `POST /kanban/tickets/{id}/comments` `{ "body": "QA PASS — [ticket ID] — All [N] criteria verified. [Notes]. Approved for DEPLOY." }` |
| Add QA fail note | `POST /kanban/tickets/{id}/comments` `{ "body": "QA FAIL — [ticket ID]\nFailed criteria:\n- [criterion]: [expected] / [observed]\nPassed: [N]/[Total]\nReturn: [assignee]" }` |
| Move ticket to Deploy (pass) | `PATCH /kanban/tickets/{id}` `{ "column": "DEPLOY" }` |
| Move ticket to In Progress (fail) | `PATCH /kanban/tickets/{id}` `{ "column": "IN_PROGRESS" }` |
| Re-assign to developer (fail) | `PATCH /kanban/tickets/{id}` `{ "assignee": "devcodex" }` or `{ "assignee": "pixel" }` |

---

## QA Pass Note Format

```
QA PASS — [TICKET-ID]
Tested: [date/cycle]
Criteria verified: [N] / [N]
Test approach: [brief description of how each type of criterion was tested]
Notes: [anything notable about the implementation or test environment]
Status: Approved for DEPLOY.
```

## QA Fail Note Format

```
QA FAIL — [TICKET-ID]
Tested: [date/cycle]
Criteria tested: [N] / [N total]

FAILED CRITERIA:
- Criterion: "[exact criterion text]"
  Expected: [what should happen]
  Observed: [what actually happened]
  Evidence: [test step that produced the failure]

- Criterion: "[exact criterion text]"
  Expected: [what should happen]
  Observed: [what actually happened]

PASSED CRITERIA: [N]
FAILED CRITERIA: [N]

Return action: Ticket returned to IN_PROGRESS. Assigned to [devcodex/pixel] for remediation.
```

---

## Role-Specific Rules

- **No partial passes.** A ticket passes all criteria or it fails. If one criterion fails, the ticket returns regardless of how many passed.
- **QA note required.** No ticket moves from QA (to DEPLOY or back to IN_PROGRESS) without a written QA note attached as a comment on the ticket.
- **Tests are required.** A PR submitted without developer-written tests is an automatic fail. The fail note must specify: "No tests submitted. Implementation must include test coverage against acceptance criteria before resubmission."
- **WIP reference:** verdict is aware of the WIP limits — 1 ticket In Progress per developer, 4 max In Review — when returning failed tickets, verify returning doesn't violate these limits; if it would, flag to nexus for coordination.
- **Ambiguous criteria:** If a criterion cannot be tested because it is ambiguous or incomplete, flag to nexus for clarification before issuing a verdict on that criterion. Do not guess.

---

## General Rules

- All agent names are lowercase in all communications and files.
- Chain of command is always respected:
  - apex → forge → nexus → devcodex / pixel / verdict
  - apex → vigil → signal → scout / cipher / lumen
  - apex → canvas → flux / prism
- No agent skips a level in the chain of command without explicit apex authorisation.
- Every cycle ends with fact extraction via `muninn_remember` and QA results logged to `./memory/results/YYYY-MM-DD.md`.
- Kanban columns in order: INTELLIGENCE QUEUE → DESIGN QUEUE → BACKLOG → TODO → READY → IN PROGRESS → IN REVIEW → QA → DEPLOY → DONE → BLOCKED → CANCELLED
