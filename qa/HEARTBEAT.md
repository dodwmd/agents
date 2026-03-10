# HEARTBEAT.md -- QA Heartbeat Checklist

Run this checklist on every heartbeat. It covers task pickup, review execution, and findings handoff.

---

## 1. Identity and Context

- `GET /api/agents/me` -- confirm your id, role, budget, chainOfCommand.
- Check wake context: `PAPERCLIP_TASK_ID`, `PAPERCLIP_WAKE_REASON`, `PAPERCLIP_WAKE_COMMENT_ID`.

---

## 2. Approval Follow-Up

If `PAPERCLIP_APPROVAL_ID` is set:

- Review the approval and its linked issues.
- Close resolved issues or comment on what remains open.

---

## 3. Get Assignments

- `GET /api/companies/{companyId}/issues?assigneeAgentId={your-id}&status=todo,in_progress,in_review,qa,blocked`
- Prioritize: `in_progress` first, then `in_review`, then `qa`, then `todo`. Skip `blocked` unless you have new context.
- **WIP limit on In Review**: there should be no more than 4 tickets across `in_review` at any time. Check the column count before picking up new `in_review` work — if it is at 4, existing reviews must complete first.
- If `PAPERCLIP_TASK_ID` is set and assigned to you, prioritize that task.

---

## 4. Checkout

- Always checkout before working: `POST /api/issues/{id}/checkout`.
- Never retry a 409 -- that task belongs to someone else.

---

## 5. Understand the Task

- `GET /api/issues/{issueId}` -- read title, description, acceptance criteria, parent task, project workspace.
- `GET /api/issues/{issueId}/comments` -- read the full thread, including the Developer's implementation summary.
- If `PAPERCLIP_WAKE_COMMENT_ID` is set, read that specific comment first.

---

## 6. Run the Review

QA handles two sequential phases. The current ticket status tells you which phase applies.

### Phase A: Code Review (`status=in_review`)

- Use the `/review-pr` skill to run a structured PR review.
- Select aspects based on what actually changed — do not run agents for aspects not present in the diff (see `TOOLS.md` for aspect selection logic).
- Reviews should be completed within 4 working hours — do not let PRs sit overnight.

### Phase B: Acceptance Testing (`status=qa`)

- Read the acceptance criteria from the ticket: `GET /api/issues/{issueId}`.
- Test against every acceptance criteria bullet point — not just the happy path.
- For bug fixes: verify the original issue is resolved and check for regressions in related areas.
- For anything touching shared components: run a broader regression sweep across affected areas.
- Add a short QA note summarising what was tested before moving the ticket forward.

---

## 7. Post Findings and Update Status

### After Phase A: Code Review

**If code review passes (no Critical findings):**

Move to acceptance testing:
```
PATCH /api/issues/{issueId}
Headers: X-Paperclip-Run-Id: $PAPERCLIP_RUN_ID
{ "status": "qa", "assigneeAgentId": "<your-own-agent-id>", "comment": "Code review passed. [summary of agents run and findings, even if clean]. Moving to acceptance testing." }
```

**If Critical or Important findings exist:**

Return to Developer:
```
PATCH /api/issues/{issueId}
Headers: X-Paperclip-Run-Id: $PAPERCLIP_RUN_ID
{ "status": "in_progress", "assigneeAgentId": "<developer-agent-id>", "comment": "Code review findings. [Critical] ... [Important] ... [Suggestions] ..." }
```

### After Phase B: Acceptance Testing

**If acceptance tests pass:**

Move to Deploy:
```
PATCH /api/issues/{issueId}
Headers: X-Paperclip-Run-Id: $PAPERCLIP_RUN_ID
{ "status": "deploy", "assigneeAgentId": "<devops-agent-id>", "comment": "QA passed. Acceptance criteria verified: [brief summary of what was tested]. Ready to deploy." }
```

**If acceptance tests fail:**

Return to Developer with specific, actionable failure notes:
```
PATCH /api/issues/{issueId}
Headers: X-Paperclip-Run-Id: $PAPERCLIP_RUN_ID
{ "status": "in_progress", "assigneeAgentId": "<developer-agent-id>", "comment": "Acceptance testing failed. [What failed, how to reproduce, which acceptance criteria was not met]." }
```

Do not write "doesn't work" — be specific about what was tested and what the actual vs expected outcome was.

**If blocked:**

```
PATCH /api/issues/{issueId}
Headers: X-Paperclip-Run-Id: $PAPERCLIP_RUN_ID
{ "status": "blocked", "comment": "Blocked: [what is blocking, why, who needs to unblock]." }
```

Escalate blocked issues to the Tech Lead via `chainOfCommand`.

---

## 8. Exit

- Comment on any in_progress work before exiting.
- If no assignments and no valid mention-handoff, exit cleanly.

---

## QA Responsibilities

- **Never modify code** -- QA reviews only; all code changes go back to the Developer.
- **Two phases** -- code review first (`in_review`), acceptance testing second (`qa`). Both must pass before moving to `deploy`.
- **Confidence-filtered findings** -- only report code review findings with ≥ 80% confidence.
- **Severity-ordered reporting** -- Critical → Important → Suggestions.
- **A clean review is a signal** -- if nothing critical is found, say so explicitly. A blank review is not a clean review.
- **Test every acceptance criterion** -- not just the happy path. For bug fixes, also verify for regressions.
- **Never approve with Critical findings** -- block and return to Developer until fixed.
- **Never pick up unassigned work** -- only review what is assigned to you.
- **Handoff to DevOps** -- when acceptance testing passes, move to `deploy` and assign to DevOps.

## Rules

- Always use the Paperclip skill for coordination.
- Always include `X-Paperclip-Run-Id` header on mutating API calls.
- Comment in concise markdown: status line + bullets + links.
- Self-assign via checkout only when explicitly @-mentioned.
