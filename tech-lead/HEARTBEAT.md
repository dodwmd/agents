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

**Labels are the tracking mechanism for triage state.** There are no `triaged` or `needs-info` issue fields — these are labels. Before doing any triage work, resolve the label UUIDs (create them if missing):

```bash
LABELS=$(curl -s "$PAPERCLIP_API_URL/api/companies/$PAPERCLIP_COMPANY_ID/labels" \
  -H "Authorization: Bearer $PAPERCLIP_API_KEY")

TRIAGED_ID=$(echo "$LABELS" | jq -r '.[] | select(.name=="triaged") | .id')
NEEDS_INFO_ID=$(echo "$LABELS" | jq -r '.[] | select(.name=="needs-info") | .id')

# Create any that are missing
if [ -z "$TRIAGED_ID" ] || [ "$TRIAGED_ID" = "null" ]; then
  TRIAGED_ID=$(curl -s -X POST "$PAPERCLIP_API_URL/api/companies/$PAPERCLIP_COMPANY_ID/labels" \
    -H "Authorization: Bearer $PAPERCLIP_API_KEY" \
    -H "X-Paperclip-Run-Id: $PAPERCLIP_RUN_ID" \
    -H "Content-Type: application/json" \
    -d '{"name":"triaged","color":"#10b981"}' | jq -r '.id')
fi

if [ -z "$NEEDS_INFO_ID" ] || [ "$NEEDS_INFO_ID" = "null" ]; then
  NEEDS_INFO_ID=$(curl -s -X POST "$PAPERCLIP_API_URL/api/companies/$PAPERCLIP_COMPANY_ID/labels" \
    -H "Authorization: Bearer $PAPERCLIP_API_KEY" \
    -H "X-Paperclip-Run-Id: $PAPERCLIP_RUN_ID" \
    -H "Content-Type: application/json" \
    -d '{"name":"needs-info","color":"#f59e0b"}' | jq -r '.id')
fi
```

Run triage over all backlog items that do **not** have the `triaged` label. Fetch all backlog issues and filter client-side:

```bash
# Get all backlog issues
ALL_BACKLOG=$(curl -s "$PAPERCLIP_API_URL/api/companies/$PAPERCLIP_COMPANY_ID/issues?status=backlog" \
  -H "Authorization: Bearer $PAPERCLIP_API_KEY")

# Filter: issues where labelIds does not contain TRIAGED_ID
UNTRIAGED=$(echo "$ALL_BACKLOG" | jq --arg tid "$TRIAGED_ID" \
  '[.[] | select(.labelIds | index($tid) == null)]')
```

For each untriaged item, work through the triage checklist:

### 5a. Classify
- Is it a **bug** (something broken), **feature** (new capability), **tech-debt** (internal improvement), or **security/compliance** issue?
- Update `type` on the issue if mis-classified at intake.

### 5b. Route
- Does it touch a **shared component or platform infrastructure**? → assign to Platform Team.
- Does it belong to a specific **squad or project**? → set `projectId` accordingly.
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

If **insufficient information** → apply the `needs-info` label, comment what's missing, notify requester. Do **not** proceed to 5e.

```bash
# Apply needs-info label (read current labelIds first to avoid wiping other labels)
CURRENT_LABELS=$(curl -s "$PAPERCLIP_API_URL/api/issues/$ISSUE_ID" \
  -H "Authorization: Bearer $PAPERCLIP_API_KEY" | jq -r '.labelIds')
NEW_LABELS=$(echo "$CURRENT_LABELS" | jq --arg id "$NEEDS_INFO_ID" '. + [$id] | unique')

curl -s -X PATCH "$PAPERCLIP_API_URL/api/issues/$ISSUE_ID" \
  -H "Authorization: Bearer $PAPERCLIP_API_KEY" \
  -H "X-Paperclip-Run-Id: $PAPERCLIP_RUN_ID" \
  -H "Content-Type: application/json" \
  -d "{\"labelIds\": $NEW_LABELS, \"comment\": \"Needs more info: [explain what is missing]\"}"
```

### 5e. Mark triaged
Apply the `triaged` label and set `status=todo`. Items in `todo` are acknowledged and understood, but not yet fully refined.

```bash
# Apply triaged label + advance to todo in one PATCH
CURRENT_LABELS=$(curl -s "$PAPERCLIP_API_URL/api/issues/$ISSUE_ID" \
  -H "Authorization: Bearer $PAPERCLIP_API_KEY" | jq -r '.labelIds')
NEW_LABELS=$(echo "$CURRENT_LABELS" | jq --arg id "$TRIAGED_ID" '. + [$id] | unique')

curl -s -X PATCH "$PAPERCLIP_API_URL/api/issues/$ISSUE_ID" \
  -H "Authorization: Bearer $PAPERCLIP_API_KEY" \
  -H "X-Paperclip-Run-Id: $PAPERCLIP_RUN_ID" \
  -H "Content-Type: application/json" \
  -d "{\"status\": \"todo\", \"labelIds\": $NEW_LABELS, \"comment\": \"Triage complete. [brief rationale]\"}"
```

Log the triage decision to `$AGENT_HOME/memory/backlog/YYYY-MM-DD.md`.

### 5f. Refinement: Todo → Ready
For each `status=todo` item, complete the refinement checklist:

1. **Check if AC already exists.** Read the issue description and comments. If the Product Owner has already written acceptance criteria (look for an "## Acceptance Criteria" section or equivalent), skip to step 3.
2. **Write acceptance criteria.** A short, testable bullet list of what "done" looks like. Add it to the issue description via PATCH.
3. **Request PO confirmation** via a comment: post the AC (or a link to where it was added) and @-mention the Product Owner. Example: `@product-owner please confirm these acceptance criteria match intent before I move this to ready.`
4. **Wait for PO sign-off.** Do NOT move to `ready` until the PO has confirmed in a comment. If you were woken by a PO comment confirming AC, proceed immediately to step 5.
5. **Tag dependencies.** Note any design or ticket dependencies (leave in `todo` if blocked on another ticket).
6. **Move to ready.** Set `status=ready` with a comment: `Refinement complete. AC confirmed by PO. Moving to ready.`

> **Important:** Write AC yourself — do not wait for the PO to write them. The PO's job is to confirm your AC matches product intent, not to author it. If you see the PO has already written AC (they sometimes do), treat that as a draft: review it for completeness, then request their confirmation and move on.

Once acceptance criteria are confirmed by the Product Owner, set `status=ready`.

Keep the `ready` column healthy: aim for 5–10 tickets at all times. If it exceeds 10, stop refining and let Developers catch up. Tickets in `ready` are ordered by priority — top of the column is highest priority.

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
