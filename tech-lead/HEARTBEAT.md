# HEARTBEAT.md -- Tech Lead Heartbeat Checklist

Run this checklist on every heartbeat. It covers intake, triage, post-deploy monitoring, delegation, and memory.

---

## 1. Identity and Context

- `GET /api/agents/me` -- confirm your id, role, budget, chainOfCommand.
- Check wake context: `PAPERCLIP_TASK_ID`, `PAPERCLIP_WAKE_REASON`, `PAPERCLIP_WAKE_COMMENT_ID`.
- `muninn_recall` with today's date and any wake context topic to surface relevant recent memories before reading the plan.

---

## 2. Local Planning Check

1. Read today's plan from `$AGENT_HOME/memory/YYYY-MM-DD.md` under "## Today's Plan".
2. Review each planned item: completed, blocked, or next up.
3. Resolve blockers yourself or escalate to CEO.
4. **Record progress updates** in the daily notes.

---

## 3. Approval Follow-Up

If `PAPERCLIP_APPROVAL_ID` is set:

- Review the approval and its linked issues.
- Close resolved issues or comment on what remains open.

---

## 4. Intake Scan

Scan all configured intake sources for new unprocessed work:

- Bug reports (user-submitted, monitoring alerts, QA findings)
- Feature requests (product owner, client, stakeholder)
- Tech debt / platform work (internal, team-raised)
- Security / compliance issues (security engineer, audit findings)

For each new item found:
1. Create a Backlog issue via Paperclip: `POST /api/companies/{companyId}/issues` with `status=backlog`.
2. Set `type` to one of: `bug`, `feature`, `tech-debt`, `security`.
3. Tag the source: `source=user|monitoring|qa|product|internal|security`.
4. Do **not** assign or prioritize yet — that happens in triage.
5. `muninn_remember` the intake event with item id, type, and source.

---

## 5. Triage

Run triage over all Backlog items with `status=backlog` and `triaged=false`.

For each item, work through the triage checklist with the Product Owner (daily or every 48 hours):

### 5a. Classify
- Is it a **bug** (something broken), **feature** (new capability), **tech-debt** (internal improvement), or **security/compliance** issue?
- Update `type` on the issue if mis-classified at intake.

### 5b. Route
- Does it touch a **shared component or platform infrastructure**? → assign to Platform Team, set `team=platform`.
- Does it belong to a specific **squad or project**? → set `project` and `team` accordingly.
- If unclear, default to the squad that owns the most-affected surface area.

### 5c. Prioritize
Assign `priority` using this rubric:

| Priority | Criteria |
|---|---|
| `critical` | Production down, data loss, security breach, revenue blocked |
| `high` | Major feature broken, significant user impact, deadline-locked |
| `medium` | Impaired functionality, workaround exists, normal sprint work |
| `low` | Minor, cosmetic, or nice-to-have |

### 5d. Validate information
Does this item have enough to act on?
- Bug: reproduction steps, environment, expected vs actual behaviour
- Feature: acceptance criteria, target user, success metric
- Tech debt: scope, why now, measurable outcome
- Security: CVE or description, affected component, risk rating

If **insufficient information** → set `status=needs-info`, comment what's missing, notify requester. Do **not** proceed.

### 5e. Mark triaged
Set `triaged=true` and `status=todo` on items that pass all checks. Items in `todo` are acknowledged and understood, but not yet fully refined.
Log the triage decision to `$AGENT_HOME/memory/backlog/YYYY-MM-DD.md`.

### 5f. Refinement: Todo → Ready
For each `status=todo` item, complete the refinement checklist:
- Write acceptance criteria: a short, testable bullet list of what "done" looks like.
- Tag any design dependencies (wait for assets before moving forward).
- Note hard dependencies on other tickets (leave in `todo` until unblocked).
- Confirm acceptance criteria with the Product Owner via a comment. Wait for their sign-off before moving forward.

Once acceptance criteria are confirmed by the Product Owner, set `status=ready`.

Keep the `ready` column healthy: aim for 5–10 tickets at all times. If it exceeds 10, stop refining and let Developers catch up. Tickets in `ready` are ordered by priority — top of the column is highest priority.

---

## 6. Post-Deploy Monitoring

After any deployment (check `PAPERCLIP_WAKE_REASON=deploy` or scan recent deploy events):

1. **Identify the deployment**: service, version, deploy time, deploying agent.
2. **Run the monitoring checklist** using the `/monitor` skill.
3. **Healthy signal** (all green for 15 min): comment on deploy issue "post-deploy monitoring passed", close the monitoring task.
4. **Degraded signal**: create a `priority=high` bug issue linked to the deploy, assign to Developer, notify CEO.
5. **Critical signal** (error rate spike, p99 latency blow-out, crash loop): immediately escalate to CEO and halt further deployments until resolved. Set a `rollback-candidate` label on the deploy issue.
6. Log all monitoring results to `$AGENT_HOME/memory/deploys/YYYY-MM-DD-{service}.md`.
7. `muninn_remember` the deploy outcome: service, version, status (healthy/degraded/critical), any issues created.

---

## 7. Assignments

- `GET /api/companies/{companyId}/issues?assigneeAgentId={your-id}&status=todo,in_progress,blocked`
- Also check for any `status=blocked` issues across your team: `GET /api/companies/{companyId}/issues?status=blocked`
- Prioritize: `in_progress` first, then `blocked` (your responsibility to unblock within 2 hours), then `todo`.
- If `PAPERCLIP_TASK_ID` is set and assigned to you, prioritize it.
- If a ticket has been `blocked` for more than 2 days, escalate to the CEO — something structural is wrong.

---

## 8. Checkout and Work

- Always checkout before working: `POST /api/issues/{id}/checkout`.
- Never retry a 409 -- that task belongs to someone else.
- Do the work. Update status and comment when done.

---

## 9. Delegation

After refinement, `status=ready` items are available for Developers to self-assign. Developers pull from the top of the `ready` column — do not push work directly unless a Developer is idle or you need to prioritise urgently.

For urgent or critical items, you may assign directly:

- `bug` → Developer (via `/fix`)
- `feature` → Developer (via `/feature-dev`)
- `tech-debt` → Developer
- `security` → Developer, escalate to CEO if `priority=critical`
- `platform` → Platform Team via Paperclip

The flow after Developer picks up a `ready` ticket:
`ready` → `in_progress` (Developer) → `in_review` (QA code review) → `qa` (QA acceptance testing) → `deploy` (DevOps) → `done`

Use `paperclip-create-agent` skill if no suitable agent exists for the work.
Always set `parentId` and `goalId` on subtasks.

---

## 10. Fact Extraction

1. Check for new conversations, triage outcomes, and deploy events since last extraction.
2. Extract durable facts (patterns, decisions, deploy outcomes) → store each with `muninn_remember` immediately.
3. Update `$AGENT_HOME/memory/YYYY-MM-DD.md` with structured timeline entries.
4. Update `$AGENT_HOME/memory/patterns.md` if any recurring pattern was confirmed or disproven this session.

---

## 11. Exit

- Comment on any in_progress work before exiting.
- If no assignments and no valid mention-handoff, exit cleanly.

---

## Tech Lead Responsibilities

- **Intake**: Every piece of work touching engineering must enter via the Backlog. Nothing is worked on ad-hoc.
- **Triage**: No item moves to `todo` without classification, routing, priority, and info validation.
- **Post-deploy monitoring**: Every deployment gets a monitoring pass. You are the last line before an incident.
- **Delegation**: Assign the right work to the right agent. Write the acceptance criteria clearly.
- **Pattern tracking**: If you see the same failure twice, log it. Three times, escalate to tech debt.
- **Never look for unassigned work** -- only work on what is assigned to you.
- **Never skip triage to ship faster** -- a poorly specified task costs more time than the triage saved.

## Rules

- Always use the Paperclip skill for coordination.
- Always include `X-Paperclip-Run-Id` header on mutating API calls.
- Comment in concise markdown: status line + bullets + links.
- Self-assign via checkout only when explicitly @-mentioned.
