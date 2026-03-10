# PropertyDuo Agents

AI agent definitions for [Paperclip](https://github.com/paperclipai/paperclip) — the open-source control plane for AI-agent companies.

This repo defines a five-agent org operating a full Kanban flow: a CEO that sets direction, a Product Owner that owns the backlog, a Tech Lead that owns triage and refinement, a Developer that builds, a QA engineer that reviews and tests, and a DevOps engineer that deploys.

---

## Agents

| Agent | Role | Directory |
|---|---|---|
| **CEO** | Strategic planning, delegation, hiring, budget oversight | `ceo/` |
| **Product Owner** | Backlog intake, acceptance criteria review, Done review, cancellations | `product-owner/` |
| **Tech Lead** | Triage, refinement, post-deploy monitoring, unblocking | `tech-lead/` |
| **Developer** | Feature development, bug fixes, technical implementation | `developer/` |
| **QA** | Code review (In Review) and acceptance testing (QA) | `qa/` |
| **DevOps** | CI/CD deployment, smoke testing, rollbacks | `devops/` |

Each agent directory contains:

```
<agent>/
  SOUL.md       # Persona, values, and operating principles
  TOOLS.md      # Skills and agents reference
  AGENTS.md     # Coordination context: chain of command, memory, safety
  HEARTBEAT.md  # Step-by-step heartbeat checklist
  .claude/
    skills/     # Slash command workflows
    agents/     # Subagent definitions
    standards/  # Non-negotiable quality, security, and process standards (some agents)
```

---

## Kanban Flow

Tickets move through these columns, each owned by a specific agent:

| Column | Owner | WIP Limit | Action |
|---|---|---|---|
| **Backlog** | Product Owner | — | Intake and describe |
| **Todo** | Tech Lead | — | Refine and write acceptance criteria |
| **Ready** | Tech Lead (fill) / Developer (pull) | 5–10 | Prioritised and AC-confirmed |
| **In Progress** | Developer | 2 per developer | Build on feature branch |
| **In Review** | QA (code review) | 4 total | Review PR; pass to QA or return to Developer |
| **QA** | QA (acceptance testing) | — | Test against criteria; pass to Deploy or return |
| **Deploy** | DevOps | — | Pipeline, smoke test; pass to Done or return to QA |
| **Done** | Product Owner + Tech Lead | — | Confirm outcome; scan for patterns |
| **Blocked** | Developer (raises) / Tech Lead (resolves) | — | Unblock within 2 hours |
| **Cancelled** | Product Owner | — | Archive with reason |

Status flow: `backlog` → `todo` → `ready` → `in_progress` → `in_review` → `qa` → `deploy` → `done`

---

## How It Works

Paperclip orchestrates agents via **heartbeats** — short execution windows triggered on a schedule or by events (task assignment, @-mentions, approvals). Each heartbeat, an agent wakes, checks its assigned work, acts, and exits.

Paperclip injects these environment variables at runtime — no manual configuration needed per run:

| Variable | Purpose |
|---|---|
| `PAPERCLIP_AGENT_ID` | This agent's identity |
| `PAPERCLIP_COMPANY_ID` | The company this agent belongs to |
| `PAPERCLIP_API_URL` | Paperclip API base URL |
| `PAPERCLIP_API_KEY` | Short-lived JWT for this run |
| `PAPERCLIP_RUN_ID` | Run audit trail ID — must be sent on all mutating API calls |
| `PAPERCLIP_TASK_ID` | Issue that triggered this wake (if event-based) |
| `PAPERCLIP_WAKE_REASON` | Why this run was triggered |
| `PAPERCLIP_WAKE_COMMENT_ID` | Specific comment that triggered this wake |
| `PAPERCLIP_APPROVAL_ID` | Pending approval to resolve (if applicable) |
| `PAPERCLIP_APPROVAL_STATUS` | `approved` or `rejected` (when wake is approval resolution) |
| `PAPERCLIP_LINKED_ISSUE_IDS` | Comma-separated issue IDs linked to a resolved approval |
| `AGENT_HOME` | Agent's personal directory (e.g. `./ceo`) |

---

## CEO Agent

The CEO owns strategic direction, budget, and org health. It manages the Tech Lead and Product Owner.

**Skills** (`.claude/skills/`):

| Skill | Purpose |
|---|---|
| `/strategy <topic>` | Multi-angle strategic analysis → recommendation → decision log |
| `/board <topic>` | Independent advisor deliberation → synthesis → CEO decides |
| `/war-room <scenario>` | Compound risk modeling (max 3 variables) → hedging plan |
| `/delegate <task>` | Creates well-defined Paperclip tasks assigned to the right agent |
| `/intel <focus>` | Competitive intelligence sweep → actionable CEO briefing |

**Subagents** (`.claude/agents/`):

| Agent | Purpose |
|---|---|
| `strategic-analyst` | Analyzes through one lens (market / capital / people / product) |
| `devil-advocate` | Stress-tests proposals; finds failure modes and bad assumptions |
| `org-analyst` | Team capacity, retention risk, cultural impact, execution confidence |

**Memory**: Uses the `para-memory-files` skill for persistent PARA-structured memory across sessions. Daily notes, entity knowledge graph, and decision logs all live under `$AGENT_HOME/memory/`.

---

## Product Owner Agent

The Product Owner owns the Backlog, acceptance criteria review, Done review, and cancellations.

**Kanban columns owned**: Backlog (intake), Todo (AC review), Done (weekly review), Cancelled

**Memory**: Uses `muninndb` for product decisions and stakeholder context. PARA files for intake logs, decision logs, and cancellation records.

---

## Tech Lead Agent

The Tech Lead owns intake triage, ticket refinement, post-deploy monitoring, and unblocking developers. Manages Developer, QA, and DevOps.

**Skills** (`.claude/skills/`):

| Skill | Purpose |
|---|---|
| `/intake` | Processes raw incoming work and creates Backlog issues |
| `/triage` | Processes untriaged Backlog items (daily / every 48 hours with Product Owner) |
| `/monitor <service>` | Post-deploy monitoring pass — confirms healthy signal or creates incident issues |
| `/pattern-review` | Analyzes issues to surface recurring failure patterns |

**Subagents** (`.claude/agents/`):

| Agent | Purpose |
|---|---|
| `intake-classifier` | Classifies raw work items by type and source |
| `triage-router` | Determines correct squad, project, and team assignment |
| `info-validator` | Validates work items for completeness before refinement |
| `deploy-monitor` | Monitors error rate, latency, and health signals post-deploy |
| `pattern-analyst` | Groups issues by component and frequency to identify tech debt |

**Memory**: Uses `muninndb` for triage decisions, deploy outcomes, and recurring patterns. PARA files for triage logs, deploy monitor logs, and patterns.

---

## Developer Agent

The Developer implements features and fixes bugs using a structured, gate-based workflow. Pulls from the `ready` column with a WIP limit of 2 in-progress tickets at once.

**Skills** (`.claude/skills/`):

| Skill | Purpose |
|---|---|
| `/feature-dev <description>` | 8-phase feature workflow: discover → explore → clarify → design → implement → test → review → summarize |
| `/fix <description>` | 8-phase bug-fix workflow: triage → investigate → confirm → design → fix → regress → review → summarize |

**Subagents** (`.claude/agents/`):

| Agent | Purpose |
|---|---|
| `code-explorer` | Deep codebase exploration; returns key files and architecture map |
| `code-architect` | Designs implementation approaches with different trade-off lenses |
| `code-tester` | Writes tests following existing project test patterns |
| `code-reviewer` | Reviews code with a specific lens (simplicity / bugs / conventions) |
| `bug-investigator` | Traces execution paths to root cause from two angles in parallel |

**Standards** (`.claude/standards/`): `git.md`, `quality.md`, `security.md`

**External Skills**: `paperclip` (task coordination — checkout, status, comments, handoff)

---

## QA Agent

The QA engineer handles two sequential phases: code review (`in_review`) and acceptance testing (`qa`). On pass, moves to `deploy`. On fail, returns to `in_progress` with specific findings. WIP limit of 4 on the In Review column.

**Skills** (`.claude/skills/`):

| Skill | Purpose |
|---|---|
| `/review-pr [aspects]` | Comprehensive PR review using targeted subagents per change type |

Aspects: `all` (default), `comments`, `tests`, `errors`, `types`, `code`, `simplify`

**Subagents** (`.claude/agents/`):

| Agent | Purpose |
|---|---|
| `code-reviewer` | General code review; confidence-scored 0–100 |
| `pr-test-analyzer` | Test coverage quality and behavioral gap analysis |
| `silent-failure-hunter` | Catches swallowed exceptions and hidden failures |
| `type-design-analyzer` | Rates type design on encapsulation, invariants, usefulness |
| `comment-analyzer` | Comment accuracy, rot, and misleading documentation |
| `code-simplifier` | Polish pass — reduces complexity without changing behavior |

**Standards** (`.claude/standards/`): `quality.md`, `security.md`, `documentation.md`

**External Skills**: `paperclip` (task coordination — checkout, status, comments, handoff)

---

## DevOps Agent

The DevOps engineer owns the Deploy column. Triggers the CI/CD pipeline, runs smoke tests, and moves tickets to Done or returns them to QA with specific failure notes.

**Kanban columns owned**: Deploy

**Memory**: Uses `muninndb` for deploy history per service. PARA files for post-deploy logs under `$AGENT_HOME/memory/deploys/`.

---

## Prerequisites

1. A running Paperclip instance — see [paperclipai/paperclip](https://github.com/paperclipai/paperclip) for setup
2. Each agent registered in Paperclip under your company
3. Agents connected via Claude Code (local adapter) or OpenClaw gateway
4. `AGENT_HOME` pointing to the relevant agent directory

To register an agent and get its environment variables:

```bash
paperclipai agent local-cli <agent-id-or-shortname> --company-id <company-id>
```

---

## Paperclip Skills

The `paperclip`, `para-memory-files`, and `paperclip-create-agent` skills are sourced from the [Paperclip repo](https://github.com/paperclipai/paperclip/tree/main/skills). They are referenced by the agents but not stored here — install them per the Paperclip documentation.
