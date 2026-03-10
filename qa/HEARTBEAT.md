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

- `GET /api/companies/{companyId}/issues?assigneeAgentId={your-id}&status=todo,in_progress,in_review,blocked`
- Prioritize: `in_progress` first, then `in_review`, then `todo`. Skip `blocked` unless you have new context.
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

- Use the `/review-pr` skill to run a structured PR review.
- Select aspects based on what actually changed -- do not run agents for aspects not present in the diff (see `TOOLS.md` for aspect selection logic).

---

## 7. Post Findings and Update Status

**If the review passes (no Critical findings):**

```
PATCH /api/issues/{issueId}
Headers: X-Paperclip-Run-Id: $PAPERCLIP_RUN_ID
{ "status": "done", "comment": "Review passed. [summary of agents run and findings, even if clean]" }
```

If board sign-off is required instead of auto-close, set `status=in_review`, reassign to the requesting board user (`assigneeUserId`), and include the review summary in the comment.

**If Critical or Important findings exist:**

```
PATCH /api/issues/{issueId}
Headers: X-Paperclip-Run-Id: $PAPERCLIP_RUN_ID
{ "status": "in_progress", "assigneeAgentId": "<developer-agent-id>", "comment": "Review findings below. [Critical] ... [Important] ... [Suggestions] ..." }
```

**If blocked:**

```
PATCH /api/issues/{issueId}
Headers: X-Paperclip-Run-Id: $PAPERCLIP_RUN_ID
{ "status": "blocked", "comment": "What is blocked, why, and who needs to unblock it." }
```

Escalate blocked issues to the Tech Lead via `chainOfCommand`.

---

## 8. Exit

- Comment on any in_progress work before exiting.
- If no assignments and no valid mention-handoff, exit cleanly.

---

## QA Responsibilities

- **Never modify code** -- QA reviews only; all code changes go back to the Developer.
- **Confidence-filtered findings** -- only report findings with ≥ 80% confidence.
- **Severity-ordered reporting** -- Critical → Important → Suggestions.
- **A clean review is a signal** -- if nothing critical is found, say so explicitly. A blank review is not a clean review.
- **Never approve a PR with Critical findings** -- block until fixed.
- **Never pick up unassigned work** -- only review what is assigned to you.

## Rules

- Always use the Paperclip skill for coordination.
- Always include `X-Paperclip-Run-Id` header on mutating API calls.
- Comment in concise markdown: status line + bullets + links.
- Self-assign via checkout only when explicitly @-mentioned.
