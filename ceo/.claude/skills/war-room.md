---
description: Scenario war room for compound risk analysis. Models what happens when multiple adverse conditions hit simultaneously. Use when facing existential or compound risks — not single-variable stress tests.
---

You are running a scenario war room. This models compound adversity — multiple bad things happening together. Maximum 3 variables per scenario.

## Core Principle

"Single-variable stress tests are for finance models. War rooms are for survival." If X AND Y both happen simultaneously, what does the company do?

---

## Phase 1: Scenario Definition

**Scenario**: $ARGUMENTS

**Actions**:
1. Identify the scenario variables (strict maximum: 3). Examples:
   - Runway < 6 months AND a key engineer resigns
   - Largest customer churns AND fundraise stalls
   - Regulatory change AND competitor raises $10M
2. Confirm variables with CEO — reject framing beyond 3 variables
3. Set severity levels to model: Base / Stress / Severe

---

## Phase 2: Domain Impact Mapping

Launch **3–4 `org-analyst` agents in parallel**, each covering one domain:

- **Revenue/customers**: ARR at risk, churn acceleration, pipeline impact, sales cycle effects
- **Team/people**: Retention risk, hiring freeze implications, morale, key-person dependencies
- **Capital/runway**: Burn rate changes, fundraise probability shift, cost cut options, payback timeline
- **Product/tech**: Delivery slowdown, tech debt pressure, competitive gap risk

Each agent:
- Models impact at Base / Stress / Severe with specific numbers
- Identifies trigger metrics (measurable signals the scenario is materializing)
- Proposes domain-specific hedges

---

## Phase 3: Cascade Analysis

**Goal**: Map how domain impacts compound each other.

**Actions**:
1. Trace the cascade: how does a revenue hit trigger a people problem? How does a people problem accelerate the revenue problem?
2. Identify the critical path of failure
3. Find the intervention point — where does breaking the cascade have the most leverage?
4. Present cascade map to CEO

---

## Phase 4: Hedging Strategy

**Actions**:
1. Define 3 early warning signals (specific metrics, not vibes)
2. Design 3 proactive hedges to execute NOW (before scenario materializes):
   - Each hedge: action, owner, deadline, estimated cost
3. Design 3 reactive responses to execute IF scenario materializes:
   - Each response: trigger metric threshold, action, owner, decision deadline
4. Present to CEO for approval

---

## Phase 5: Exit

**Actions**:
1. Log scenario and approved hedges to `$AGENT_HOME/memory/YYYY-MM-DD.md`
2. Assign early warning signal monitoring to appropriate agent
3. Set review date: when to re-assess this scenario
4. Present one-page summary: scenario, top 3 hedges, early warning signals, review date
