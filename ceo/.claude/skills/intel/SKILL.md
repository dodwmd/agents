---
name: intel
description: Competitive intelligence workflow. Gathers and synthesizes competitor signals into an actionable CEO briefing. Run before major strategic decisions or on a monthly cadence.
---

You are running a competitive intelligence sweep. Focus on intelligence that changes decisions — not just what's interesting.

## Core Principle

"Competitive obsession loses; competitive awareness wins." Track enough to avoid blind spots, not so much you start copying competitors.

---

## Phase 1: Scope

**Focus**: $ARGUMENTS

**Actions**:
1. Define scope with CEO:
   - Which competitors? (direct / indirect / emerging)
   - Which signals? (pricing, product moves, funding, hiring, customer wins, messaging)
   - What decision will this intelligence inform?
2. Set recency window (default: last 30 days)
3. Load prior intel from `$AGENT_HOME/memory/intel/` to avoid reporting stale signals

---

## Phase 2: Intelligence Gathering

Launch **2–3 `org-analyst` agents in parallel**, each covering a competitor cluster.

Each agent tracks across 8 dimensions:
1. Product moves (new features, deprecations, positioning shifts)
2. Pricing changes
3. Funding events
4. Key hires / departures
5. Customer wins / losses (public references, G2/Capterra reviews)
6. Partnership announcements
7. Messaging shifts (website, ads, content)
8. Sales tactics (pricing pressure, discounting, bundling)

Each finding must include: source, date, confidence tag ([VERIFIED] / [INFERRED] / [RUMORED]).

---

## Phase 3: Synthesis

**Actions**:
1. Filter to signals that change a decision — discard the rest
2. Identify patterns across signals (e.g., competitor is repositioning upmarket)
3. Rate threat level per competitor: Low / Elevated / High / Urgent
4. Produce CEO briefing:
   - **Top 3 actionable signals** — what changes in our plans because of this?
   - **Emerging threats** — who is becoming relevant that wasn't before?
   - **Opportunities** — gaps competitors are leaving open
   - **Recommended response** for each High / Urgent signal

---

## Phase 4: Distribution and Logging

**Actions**:
1. Log to `$AGENT_HOME/memory/intel/YYYY-MM-DD.md`
2. Flag for delegation if signal requires action:
   - Competitor pricing change → consider delegating pricing review to developer
   - Product gap opportunity → consider creating roadmap task
3. Set next intel run date (default: 30 days unless a High/Urgent signal warrants sooner)
