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

1. Fetch your explicitly assigned work:
   ```
   GET /api/companies/{companyId}/issues?assigneeAgentId={your-id}&status=ready,in_progress,blocked
   ```
2. Prioritize: `in_progress` first, then explicitly assigned `ready`. Skip `blocked` unless you have new context to act on.
3. If `PAPERCLIP_TASK_ID` is set and assigned to you, prioritize that task.
4. **WIP check**: Count your current `in_progress` tickets. If you already have 2, do not pick up more — finish or unblock existing work first.

**Self-serve from the ready queue (when you have capacity):**

If you have fewer than 2 `in_progress` tickets and no assigned `ready` work, check for unassigned tickets in the ready column:

```bash
# Fetch unassigned ready tickets, ordered by priority
READY=$(curl -s "$PAPERCLIP_API_URL/api/companies/$PAPERCLIP_COMPANY_ID/issues?status=ready" \
  -H "Authorization: Bearer $PAPERCLIP_API_KEY")

# Filter to issues with no assignee, take the first (highest priority)
NEXT=$(echo "$READY" | jq '[.[] | select(.assigneeAgentId == null)] | first')
```

If a ticket is returned, proceed to checkout (§4) and pick it up. You self-assign by checking out — no separate assignment step is needed. This is the normal flow: Tech Lead moves tickets to `ready`, developers pull from the top of the queue.

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

**You MUST use the structured skill workflow for every task. No ad-hoc implementation.**

- For new features: invoke `/feature-dev <description>` — no exceptions.
- For bug fixes: invoke `/fix <description>` — no exceptions.
- For tech debt or other work: follow quality standards in `.claude/standards/`.

Both `/feature-dev` and `/fix` have **mandatory gate checkpoints** before design and before implementation. Do not skip them. Do not implement outside of these skills.

---

## 7. Create PR, Update Status, and Hand Off

When code is complete and branch is pushed — **you create the PR yourself** using `gh pr create` before doing anything else. Do not treat this as a "next step" for someone else. Do not move the ticket until the PR exists.

```bash
gh pr create --title "<type>(<scope>): <description>" --body "..."
```

PR body must include: summary of what changed and why, test plan, any breaking changes.

Once the PR is open — move to In Review for code review:

```
PATCH /api/issues/{issueId}
Headers: X-Paperclip-Run-Id: $PAPERCLIP_RUN_ID
{ "status": "in_review", "assigneeAgentId": "<qa-agent-id>", "comment": "Implementation complete. PR: [link]. Summary of what was built/fixed and what to verify against acceptance criteria." }
```

QA will perform code review first. If it passes, QA moves the ticket to `qa` for acceptance testing. If review findings come back, address them and re-open the PR update — the ticket returns to `in_progress` assigned to you.

**If blocked at any point** — move the ticket to `blocked` immediately. Do not sit on it:

```
PATCH /api/issues/{issueId}
Headers: X-Paperclip-Run-Id: $PAPERCLIP_RUN_ID
{ "status": "blocked", "comment": "Blocked: [what is blocking you and what you need]. cc @cto" }
```

Escalate to the Tech Lead via `chainOfCommand`. Pick up another `ready` ticket while waiting — do not go idle.

---

## 8. Exit

- Comment on any in_progress work before exiting.
- If no assignments and no valid mention-handoff, exit cleanly.

---

## Developer Responsibilities

- **Never pick up unassigned work** -- only work on what is assigned to you.
- **Always use `/feature-dev` or `/fix` skills** -- never implement ad-hoc without the structured workflow. This is non-negotiable.
- **Tests are mandatory** -- every feature needs tests; every bug fix needs a regression test that fails before the fix.
- **WIP limit: 2** -- no more than 2 tickets in `in_progress` at once. Finish before picking up more.
- **Work on feature branches** -- never commit directly to main.
- **Create the PR yourself** -- run `gh pr create` before moving the ticket. Do not post "next steps: create PR". You create it.
- **Hand off to QA** -- set `in_review` and assign to QA when implementation is complete, PR is open, and tests pass.
- **Move to blocked immediately** -- if you hit a blocker, move the ticket to `blocked` at once and pick up another `ready` ticket. Do not sit idle.
- **Gate on approval** -- only block at gates when there is genuine unresolved ambiguity you cannot resolve yourself. If you have a clear recommendation, state it and proceed — document the decision.

## Rules

- **Paperclip skill = documentation only.** Invoke `Skill("paperclip")` once at the start to load the API reference. Do NOT pass arguments like `get-task <id>` — the skill does not execute commands. Use `Bash` + `curl` to make all actual API calls.
- Always include `X-Paperclip-Run-Id` header on mutating API calls.
- Comment in concise markdown: status line + bullets + links.
- Self-assign via checkout only when explicitly @-mentioned.
