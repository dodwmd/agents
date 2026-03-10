# HEARTBEAT.md -- Developer Heartbeat Checklist

Run this checklist on every heartbeat. It covers task pickup, implementation, and handoff.

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

- `GET /api/companies/{companyId}/issues?assigneeAgentId={your-id}&status=todo,in_progress,blocked`
- Prioritize: `in_progress` first, then `todo`. Skip `blocked` unless you have new context to act on.
- If `PAPERCLIP_TASK_ID` is set and assigned to you, prioritize that task.

---

## 4. Checkout

- Always checkout before working: `POST /api/issues/{id}/checkout`.
- Never retry a 409 -- that task belongs to someone else.

---

## 5. Understand the Task

- `GET /api/issues/{issueId}` -- read title, description, parent task, acceptance criteria, and project workspace.
- `GET /api/issues/{issueId}/comments` -- read the full comment thread.
- Read ancestors to understand the broader goal.
- If `PAPERCLIP_WAKE_COMMENT_ID` is set, read that specific comment first.

---

## 6. Do the Work

- For new features: use the `/feature-dev` skill.
- For bug fixes: use the `/fix` skill.
- For tech debt or other work: follow quality standards in `.claude/standards/`.

Both `/feature-dev` and `/fix` have **mandatory gate checkpoints** before design and before implementation. Do not skip them.

---

## 7. Update Status and Hand Off

When work is complete and ready for review:

```
PATCH /api/issues/{issueId}
Headers: X-Paperclip-Run-Id: $PAPERCLIP_RUN_ID
{ "status": "in_review", "assigneeAgentId": "<qa-agent-id>", "comment": "Implementation complete. Summary of what was built/fixed and what to verify." }
```

If blocked at any point:

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

## Developer Responsibilities

- **Never pick up unassigned work** -- only work on what is assigned to you.
- **Always use `/feature-dev` or `/fix` skills** -- never implement ad-hoc without the structured workflow.
- **Tests are mandatory** -- every feature needs tests; every bug fix needs a regression test that fails before the fix.
- **Hand off to QA** -- set `in_review` and reassign when implementation is complete and tests pass.
- **Gate on approval** -- both skills require explicit confirmation before design and before implementation.

## Rules

- Always use the Paperclip skill for coordination.
- Always include `X-Paperclip-Run-Id` header on mutating API calls.
- Comment in concise markdown: status line + bullets + links.
- Self-assign via checkout only when explicitly @-mentioned.
