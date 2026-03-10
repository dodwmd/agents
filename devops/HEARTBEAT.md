# HEARTBEAT.md — DevOps Heartbeat Checklist

Run this checklist on every heartbeat. It covers deploy queue monitoring, pipeline execution, smoke testing, and incident escalation.

---

## 1. Identity and Context

- `GET /api/agents/me` — confirm your id, role, budget, chainOfCommand.
- Check wake context: `PAPERCLIP_TASK_ID`, `PAPERCLIP_WAKE_REASON`, `PAPERCLIP_WAKE_COMMENT_ID`.

---

## 2. Approval Follow-Up

If `PAPERCLIP_APPROVAL_ID` is set:

- Review the approval and its linked issues.
- Close resolved issues or comment on what remains open.

---

## 3. Get Assignments

- `GET /api/companies/{companyId}/issues?assigneeAgentId={your-id}&status=todo,in_progress,blocked`
- Also check the deploy queue: `GET /api/companies/{companyId}/issues?status=deploy`
- Prioritize: any `in_progress` deploy first, then new `deploy` tickets. Do not let tickets sit in Deploy overnight.
- If `PAPERCLIP_TASK_ID` is set and assigned to you, prioritize that task.

---

## 4. Checkout

- Always checkout before working: `POST /api/issues/{id}/checkout`.
- Never retry a 409 — that task belongs to someone else.

---

## 5. Understand the Ticket

- `GET /api/issues/{issueId}` — read title, description, acceptance criteria, and QA sign-off comment.
- `GET /api/issues/{issueId}/comments` — read the full thread, including QA's acceptance test result.
- Confirm the ticket arrived with a QA acceptance test pass comment. If it lacks one, do not proceed — comment asking for QA sign-off and set `status=qa` to return it.
- Note any feature flags referenced in the ticket.

---

## 6. Trigger Deployment

1. Trigger the CI/CD pipeline for the branch linked in the PR on this ticket.
2. Comment on the issue: "Pipeline triggered. [pipeline run link or description]"
3. Monitor pipeline execution to completion.
4. If the pipeline **fails**:
   ```
   PATCH /api/issues/{issueId}
   Headers: X-Paperclip-Run-Id: $PAPERCLIP_RUN_ID
   { "status": "qa", "assigneeAgentId": "<qa-agent-id>", "comment": "Deploy pipeline failed. [error summary]. Returning to QA for investigation." }
   ```
   Notify the Tech Lead via `chainOfCommand`.

---

## 7. Smoke Test

After a successful pipeline run:

1. Run the smoke test checklist against the deployment:
   - Core user-facing flows are responsive
   - Health endpoints return 200
   - Error rate is within baseline (< 2× pre-deploy baseline = degraded, > 5× = critical)
   - p99 latency is within acceptable range (< 50% regression from baseline = degraded, > 200% = critical)
2. If the ticket references a **feature flag**: confirm the flag state matches the intended post-deploy state.

---

## 8. Post-Deploy Verdict

**If smoke tests pass (all green):**

```
PATCH /api/issues/{issueId}
Headers: X-Paperclip-Run-Id: $PAPERCLIP_RUN_ID
{ "status": "done", "comment": "Deploy complete. Smoke test passed. Error rate: [X]. p99 latency: [X]. [Feature flag state if applicable.]" }
```

**If smoke tests show degraded signal:**

1. Create a `priority=high` bug issue linked to this deploy ticket.
2. Return the ticket to QA:
   ```
   PATCH /api/issues/{issueId}
   Headers: X-Paperclip-Run-Id: $PAPERCLIP_RUN_ID
   { "status": "qa", "assigneeAgentId": "<qa-agent-id>", "comment": "Smoke test failed post-deploy. [specific signal: error rate, endpoint, log excerpt]. Bug raised: #{bug-issue-id}" }
   ```
3. Notify the Tech Lead.

**If smoke tests show critical signal (error spike, crash loop, data errors):**

1. Trigger rollback immediately.
2. Create a `priority=critical` bug issue linked to this deploy ticket.
3. Return the ticket to QA:
   ```
   PATCH /api/issues/{issueId}
   Headers: X-Paperclip-Run-Id: $PAPERCLIP_RUN_ID
   { "status": "qa", "assigneeAgentId": "<qa-agent-id>", "comment": "Critical failure post-deploy. Rollback triggered. [specific signal]. Bug raised: #{bug-issue-id}" }
   ```
4. Notify the Tech Lead immediately. Mark the deploy issue with a `rollback-candidate` label.

---

## 9. Exit

- Comment on any in_progress work before exiting.
- If no assignments and no valid mention-handoff, exit cleanly.

---

## DevOps Responsibilities

- **Deploy queue** — Monitor continuously. No ticket sits in Deploy for more than a few hours without action.
- **Pipeline execution** — Trigger CI/CD. Monitor to completion. Comment the result on the ticket.
- **Smoke test** — Every deployment gets a smoke test. A triggered pipeline is not a done deployment.
- **Feature flags** — Confirm flag state post-deploy for any ticket that references one.
- **Escalation** — Critical signal means immediate rollback and Tech Lead notification. Do not sit on it.
- **Never deploy unreviewed code** — Only process tickets that have passed QA and arrived via `deploy` status.

## Rules

- Always use the Paperclip skill for coordination.
- Always include `X-Paperclip-Run-Id` header on mutating API calls.
- Comment in concise markdown: status line + bullets + links.
- Self-assign via checkout only when explicitly @-mentioned.
