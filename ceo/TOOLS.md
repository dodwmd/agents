# Tools Reference

Skills and agents available to the CEO. All files live under `.claude/`.

---

## Skills

### `/memory`
**File**: `.claude/skills/memory.md`

Reference skill defining the two-layer memory system. Consult when uncertain about whether to use muninndb or PARA files for a given operation. Contains the decision table, file formats, and when to call each muninndb tool.

---

### `/strategy <topic>`
**File**: `.claude/skills/strategy.md`

Multi-phase strategic decision workflow.

| Phase | Action | Agents used |
|-------|--------|-------------|
| 1. Context | Load prior decisions, clarify question | — |
| 2. Multi-angle analysis | Analyze across market, capital, people lenses | `strategic-analyst` ×3 parallel |
| 3. Devil's advocate | Stress-test front-runner options | `devil-advocate` ×1–3 parallel |
| 4. Synthesis | Produce ranked recommendation | — |
| 5. Decision logging | Record decision + rationale | — |

---

### `/board <topic>`
**File**: `.claude/skills/board.md`

Structured board meeting deliberation. Prevents groupthink by requiring independent contributions before synthesis.

| Phase | Action | Agents used |
|-------|--------|-------------|
| 1. Context loading | Load prior decisions, confirm roles | — |
| 2. Independent contributions | Each advisor role contributes in isolation | `strategic-analyst` ×2–4 parallel |
| 3. Critical review | Challenge consensus and assumptions | `devil-advocate` ×1 |
| 4. Synthesis | Consolidate into board output | — |
| 5. CEO decision | CEO approves, rejects, or modifies | — |
| 6. Decision logging | Log to Layer 2 (approved), Layer 1 (raw) | — |

---

### `/war-room <scenario>`
**File**: `.claude/skills/war-room.md`

Compound risk scenario analysis. Models what happens when multiple adverse conditions hit simultaneously. Max 3 variables per scenario.

| Phase | Action | Agents used |
|-------|--------|-------------|
| 1. Scenario definition | Define variables (max 3), set severity levels | — |
| 2. Domain impact mapping | Model impact across revenue, people, capital, product | `org-analyst` ×3–4 parallel |
| 3. Cascade analysis | Map how domain impacts compound | — |
| 4. Hedging strategy | Design proactive + reactive responses | — |
| 5. Exit | Log and assign monitoring | — |

---

### `/delegate <task>`
**File**: `.claude/skills/delegate.md`

Delegation workflow. Creates well-defined Paperclip tasks assigned to the right agent.

| Phase | Action | Agents used |
|-------|--------|-------------|
| 1. Task definition | Clarify outcome, constraints, decision boundaries | — |
| 2. Task creation | Create via Paperclip with full acceptance criteria | — |
| 3. Handoff confirmation | Confirm and summarize to CEO | — |

---

### `/intel <focus>`
**File**: `.claude/skills/intel.md`

Competitive intelligence sweep. Gathers competitor signals and synthesizes into an actionable briefing.

| Phase | Action | Agents used |
|-------|--------|-------------|
| 1. Scope | Define competitors, signals, and decision context | — |
| 2. Gathering | Track 8 signal dimensions per competitor cluster | `org-analyst` ×2–3 parallel |
| 3. Synthesis | Filter to decision-relevant signals, rate threat level | — |
| 4. Distribution | Log, delegate follow-on actions, set cadence | — |

---

## Agents

### `strategic-analyst`
**File**: `.claude/agents/strategic-analyst.md`
**Used by**: `/strategy` Phase 2, `/board` Phase 2

Analyzes a business decision through a specific lens (market, capital, people/org, product). Contributes independently — does not see peer outputs. Returns scored options with explicit [VERIFIED] / [ASSUMED] tagging on every claim.

---

### `devil-advocate`
**File**: `.claude/agents/devil-advocate.md`
**Used by**: `/strategy` Phase 3, `/board` Phase 3

Stress-tests strategic proposals. Identifies failure modes, suspicious consensus, unvalidated assumptions, and missing perspectives. Does not propose alternatives — only challenges existing options.

---

### `org-analyst`
**File**: `.claude/agents/org-analyst.md`
**Used by**: `/war-room` Phase 2, `/intel` Phase 2

Analyzes organizational capacity, retention risk, cultural impact, hiring requirements, and key-person dependencies. Used in war rooms for people/org domain impact and in strategy sessions for execution risk assessment.

---

## External Skills

### `muninndb` (MCP)
Episodic and semantic memory. Exposes `muninn_remember`, `muninn_recall`, `muninn_guide`, and ~35 other tools. Used for entity facts, decisions, patterns, and anything retrieved with "what do I know about X?" Memory strengthens with use and decays when unused. See `/memory` skill for when to use this vs. PARA files.

### `para-memory-files`
Referenced in `AGENTS.md`. Defines PARA folder structure and atomic fact schema conventions for structured file-based artifacts (daily notes, decision logs, intel logs). Used alongside muninndb, not instead of it. See `/memory` skill for the split.

### `paperclip`
**Documentation skill only — it loads reference instructions, it does not execute API calls.**

Invoke the `paperclip` skill once at the start of a heartbeat to load the API reference into context. After that, make every actual API call yourself using `Bash` + `curl` against `$PAPERCLIP_API_URL`. Do not pass arguments like `get-task <id>` to the skill — those are not valid commands.

Handles all organizational coordination: issue management, agent assignment, checkout, status updates, and cross-team task management — but via `curl`, not the skill itself.

### `paperclip-create-agent`
Referenced in `HEARTBEAT.md`. Used when spinning up new agents to handle capacity gaps.
