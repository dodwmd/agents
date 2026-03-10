# Tools Reference

Skills and agents available to the Tech Lead. All files live under `.claude/`.

---

## Skills

### `/intake`
**File**: `.claude/skills/intake.md`

Processes raw incoming work from all sources and creates Backlog issues in Paperclip.

| Phase | Action | Agents used |
|-------|--------|-------------|
| 1. Source scan | Poll all intake sources for new items | — |
| 2. Classification | Classify each item by type and source | `intake-classifier` ×N parallel |
| 3. Backlog creation | Create Paperclip issues with metadata | — |
| 4. Memory | Store intake events in muninndb | — |

---

### `/triage`
**File**: `.claude/skills/triage.md`

Processes all untriaged Backlog items. Runs daily or every 48 hours with the Product Owner.

| Phase | Action | Agents used |
|-------|--------|-------------|
| 1. Queue load | Fetch all `status=backlog` items; filter client-side to exclude those with the `triaged` label | — |
| 2. Classification check | Verify or correct item type | — |
| 3. Routing | Assign to squad, project, or Platform Team | `triage-router` ×1 per item |
| 4. Priority assignment | Apply priority rubric | — |
| 5. Info validation | Check for completeness; apply `needs-info` label + comment if missing | `info-validator` ×1 per item |
| 6. Mark triaged | Apply `triaged` label, set `status=todo`, log decision | — |

> **Labels are UUIDs.** There is no `triaged=true` field — apply the `triaged` label via `labelIds`. Resolve (or create) the label UUID first. See `skills/paperclip/SKILL.md` § Labels for the bootstrap pattern.

---

### `/monitor <service> [deploy-id]`
**File**: `.claude/skills/monitor.md`

Post-deploy monitoring pass for a specific service. Confirms healthy signal or creates incident issues.

| Phase | Action | Agents used |
|-------|--------|-------------|
| 1. Deploy context | Load deploy metadata: service, version, time | — |
| 2. Signal check | Query error rates, latency p99, crash loops, health endpoints | `deploy-monitor` ×2-3 parallel |
| 3. Baseline comparison | Compare against pre-deploy baseline | — |
| 4. Verdict | Healthy / Degraded / Critical | — |
| 5. Response | Comment on deploy issue, create bug if needed, escalate if critical | — |
| 6. Logging | Write to `$AGENT_HOME/memory/deploys/YYYY-MM-DD-{service}.md` | — |

---

### `/pattern-review`
**File**: `.claude/skills/pattern-review.md`

Analyzes open and recently closed issues to surface recurring failure patterns.

| Phase | Action | Agents used |
|-------|--------|-------------|
| 1. Issue scan | Load recently closed bugs + active bugs | — |
| 2. Cluster analysis | Group issues by component, error type, and frequency | `pattern-analyst` ×2 parallel |
| 3. Threshold check | Flag any pattern with ≥3 occurrences for tech debt | — |
| 4. Output | Update `$AGENT_HOME/memory/patterns.md`, create tech-debt items | — |

---

## Agents

### `intake-classifier`
**File**: `.claude/agents/intake-classifier.md`
**Used by**: `/intake` Phase 2

Classifies a raw work item by type (`bug`, `feature`, `tech-debt`, `security`) and source (`user`, `monitoring`, `qa`, `product`, `internal`, `security`). Returns classification with confidence score and reasoning. Flags ambiguous items for human review.

---

### `triage-router`
**File**: `.claude/agents/triage-router.md`
**Used by**: `/triage` Phase 3

Determines the correct squad, project, and team for a triaged issue. Checks if the issue touches a shared component or platform infrastructure (→ Platform Team). Returns `project`, `team`, `squad` assignments with rationale. Flags cross-team dependencies.

---

### `info-validator`
**File**: `.claude/agents/info-validator.md`
**Used by**: `/triage` Phase 5

Validates that a work item contains sufficient information to act on. Checks type-specific requirements:
- **Bug**: reproduction steps, environment, expected vs. actual behaviour
- **Feature**: acceptance criteria, target user, success metric
- **Tech debt**: scope, rationale, measurable outcome
- **Security**: affected component, description or CVE, risk rating

Returns `valid=true` or a specific list of missing fields with suggested questions for the requester.

---

### `deploy-monitor`
**File**: `.claude/agents/deploy-monitor.md`
**Used by**: `/monitor` Phase 2

Monitors a specific signal dimension for a deployed service. Launched in parallel with different angles:
- **Error rate**: Compare error rate against pre-deploy baseline (threshold: >2× baseline = degraded, >5× = critical)
- **Latency**: Check p99 response time (threshold: >50% regression = degraded, >200% = critical)
- **Health**: Poll health/readiness endpoints and crash loop indicators

Returns signal verdict, raw values, baseline values, and delta.

---

### `pattern-analyst`
**File**: `.claude/agents/pattern-analyst.md`
**Used by**: `/pattern-review` Phase 2

Analyzes a set of issues to identify recurring failure patterns. Groups by component, error message cluster, and recurrence frequency. Distinguishes one-off failures from systemic issues. Returns clusters sorted by frequency with affected components, issue IDs, and suggested tech debt scope.

---

## External Skills

### `muninndb` (MCP)
Episodic and semantic memory. Exposes `muninn_remember`, `muninn_recall`, `muninn_guide`, and ~35 other tools. Used for triage decisions, deploy outcomes, recurring patterns, and team capacity signals. Memory strengthens with use and decays when unused. See `AGENTS.md` for when to use this vs. PARA files.

### `para-memory-files`
Defines PARA folder structure and atomic fact schema conventions for structured file-based artifacts (triage logs, deploy monitor logs, recurring pattern files). Used alongside muninndb for structured operational memory. See `AGENTS.md` for the two-layer memory split.

### `paperclip`
**Documentation skill only — it loads reference instructions, it does not execute API calls.**

Invoke the `paperclip` skill once at the start of a heartbeat to load the API reference into context. After that, make every actual API call yourself using `Bash` + `curl` against `$PAPERCLIP_API_URL`. Do not pass arguments like `get-task <id>` to the skill — those are not valid commands.

Covers all organizational coordination: issue management, agent assignment, checkout, status updates, and cross-team task routing — but via `curl`, not the skill itself.

### `paperclip-create-agent`
Used when spinning up new agents to handle capacity gaps identified during delegation.
