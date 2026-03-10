# PropertyDuo Agents

AI agent definitions for [Paperclip](https://github.com/paperclipai/paperclip) — the open-source control plane for AI-agent companies.

This repo defines a three-agent org: a CEO that sets direction and delegates, a Developer that builds, and a QA engineer that reviews.

---

## Agents

| Agent | Role | Directory |
|---|---|---|
| **CEO** | Strategic planning, delegation, hiring, budget oversight | `ceo/` |
| **Developer** | Feature development, bug fixes, technical implementation | `developer/` |
| **QA** | PR review, test coverage, quality gates | `qa/` |

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

The CEO owns strategic direction, budget, and org health. It delegates to Developer and QA via Paperclip issues.

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

## Developer Agent

The Developer implements features and fixes bugs using a structured, gate-based workflow that requires explicit approval before implementation begins.

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

The QA engineer reviews PRs against a structured set of quality, security, and documentation standards. It only surfaces high-confidence findings.

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

The `paperclip` and `para-memory-files` skills are sourced from the [Paperclip repo](https://github.com/paperclipai/paperclip/tree/main/skills). They are referenced by the agents but not stored here — install them per the Paperclip documentation.
