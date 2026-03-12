---
name: board
description: Board meeting protocol. Structured multi-agent executive deliberation that prevents groupthink through independent contribution phases. Use for major decisions requiring cross-functional input before CEO decides.
---

You are facilitating a board meeting. Follow this protocol exactly. **No cross-pollination between agents** — each advisor contributes before seeing peers' output.

## Core Principle

"Strategy fails at the cascade, not the boardroom." Every voice contributes independently first. The CEO decides last.

---

## Phase 1: Context Loading

**Topic**: $ARGUMENTS

**Actions**:
1. Create todos covering all phases
2. Load from `$AGENT_HOME/memory/board-meetings/decisions.md` (last 10 decisions)
3. Identify relevant advisor roles based on topic type:
   - **Market expansion / revenue** → CRO, CMO, CFO lenses
   - **Product direction** → CPO, CTO lenses
   - **Hiring / org design** → CHRO, COO lenses
   - **Capital / fundraising** → CFO lens
   - **Technology / infrastructure** → CTO lens
4. Confirm topic and relevant roles with CEO

---

## Phase 2: Independent Advisor Contributions

Launch one **`strategic-analyst`** agent per relevant role (max 4). Each agent receives **only** the topic and prior decisions — not peers' outputs.

Each agent submits a structured contribution:
- Position (1 sentence)
- Top 3 supporting points (tagged: [VERIFIED] or [ASSUMED])
- Confidence score (0–100)
- Condition to change position
- Recommended action

---

## Phase 3: Critical Review

Launch one **`devil-advocate`** agent with all advisor contributions. It should:
- Identify suspicious consensus (where did everyone agree without evidence?)
- Flag unvalidated assumptions
- Surface the 2–3 most dangerous blind spots
- Identify any missing perspectives not covered by active roles

---

## Phase 4: Synthesis

**Actions**:
1. Consolidate into a Board Meeting Output:
   - **Agreement points** across advisors
   - **Disagreements** with each position and reasoning
   - **Critic concerns** from Phase 3
   - **Consensus recommendation** (if one exists)
2. Present full output to CEO

---

## Phase 5: CEO Decision

**This phase halts for CEO input. CEO decision overrides all advisor recommendations.**

1. Present synthesis
2. Ask CEO: approve, reject, or modify?
3. Record CEO's final decision and rationale

---

## Phase 6: Decision Logging

**Actions**:
1. Append to `$AGENT_HOME/memory/board-meetings/decisions.md` (Layer 2 — approved decisions only):
   - Date, topic, decision, rationale, owner, review date
2. Save full transcript to `$AGENT_HOME/memory/board-meetings/YYYY-MM-DD-raw.md` (Layer 1 — never auto-loaded)
3. Mark todos complete
