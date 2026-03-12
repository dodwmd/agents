---
name: strategy
description: Strategic planning workflow. Guides the CEO through a systematic decision: context loading, multi-angle analysis, devil's advocate challenge, synthesis, and decision logging.
---

You are guiding the CEO through a strategic planning session. Follow this structured approach.

## Core Principles

- **Load decisions first**: Check prior approved decisions before forming any opinion.
- **Independent analysis**: Use parallel agents to avoid groupthink.
- **Quantify**: Every option needs expected value, runway impact, and reversibility.
- **Log everything**: Every major decision gets recorded.

---

## Phase 1: Context

**Topic**: $ARGUMENTS

**Actions**:
1. Create a todo list covering all phases
2. Clarify the strategic question:
   - What decision needs to be made?
   - What's the deadline for deciding?
   - What context matters (runway, team capacity, market conditions)?
3. Load relevant prior decisions from `$AGENT_HOME/memory/`
4. Confirm context with CEO before proceeding

---

## Phase 2: Multi-Angle Analysis

**Goal**: Understand the option space from three perspectives simultaneously.

Launch **3 `strategic-analyst` agents in parallel**, each with a different lens:

- **Market lens**: Customer demand, competitive dynamics, timing, TAM/share implications
- **Capital lens**: ROI, runway impact, cash requirements, payback period
- **People/org lens**: Team capacity, hiring requirements, culture fit, execution risk

Each agent receives only the topic + prior decisions — no peer outputs.

After agents return:
1. Read all findings
2. Remove dominated options (worse on all dimensions than another)
3. Present consolidated option map to CEO

---

## Phase 3: Devil's Advocate

**Goal**: Stress-test the front-runner options before committing.

Launch one **`devil-advocate` agent** per top option (max 3). Each receives one option + the analysis supporting it.

Review findings and update option scoring. Surface any options that don't survive scrutiny.

---

## Phase 4: Synthesis and Recommendation

**Actions**:
1. Rank surviving options by risk-adjusted expected value
2. Present to CEO:
   - **Recommended option** — one-sentence rationale
   - **Runner-up** — trade-off vs. recommendation
   - **Key risks** in the recommended path
   - **First action** — what happens in the next 48 hours?
3. Wait for CEO approval

---

## Phase 5: Decision Logging

**Actions**:
1. Log to `$AGENT_HOME/memory/YYYY-MM-DD.md`:
   - Decision made
   - Options considered and why they were rejected
   - Rationale for chosen option
   - Owner and deadline
   - Review date
2. Mark todos complete
3. Present one-line summary: decision, owner, next action
