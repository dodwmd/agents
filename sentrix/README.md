# Sentrix — AI-Powered Market Intelligence and Trading Suggestion Platform

Sentrix is an autonomous multi-agent organisation. Each agent has a defined role, a fixed chain of command, and a set of operating rules that govern every action it takes. The agents work together across three divisions to turn raw world events into structured market intelligence, build and maintain the platform, and present that intelligence to users through a consistent, trustworthy interface.

---

## Organisation Structure

```
apex (CEO)
├── forge (CTO)
│   └── nexus (Head of Engineering)
│       ├── devcodex (Senior Developer — Claude model)
│       ├── pixel (Senior Developer — Gemini model)
│       └── verdict (QA Engineer)
├── vigil (CIO)
│   └── signal (Head of Intelligence)
│       ├── scout (World Events Monitor)
│       ├── cipher (Market Relevance Analyst)
│       └── lumen (BI Analyst)
└── canvas (Head of Design)
    ├── flux (Interface Designer)
    └── prism (Data Visualisation and Report Designer)
```

---

## Divisions

### Engineering Division
Owned by **forge**, run day-to-day by **nexus**. Responsible for all platform development — backend logic, AI integrations, frontend, data pipelines, and QA. Works from a Kanban board with strict WIP limits.

### Intelligence Division
Owned by **vigil**, coordinated by **signal**. Runs the continuous pipeline: **scout** monitors the world, **cipher** scores market relevance, **lumen** researches and confirms impact. Produces structured intelligence tickets that feed the platform.

### Design & Reporting Division
Owned by **canvas**. **flux** designs user interfaces and maintains the component library. **prism** designs data visualisations, suggestion cards, and report formats. All assets are canvas-approved before reaching engineering.

---

## Agent Directory

| Codename | Title | Division | Reports To |
|---|---|---|---|
| apex | Chief Executive Officer | C-Suite | Board |
| forge | Chief Technology Officer | Engineering | apex |
| vigil | Chief Intelligence Officer | Intelligence | apex |
| nexus | Head of Engineering | Engineering | forge |
| devcodex | Senior Developer (Claude) | Engineering | nexus |
| pixel | Senior Developer (Gemini) | Engineering | nexus |
| verdict | QA Engineer | Engineering | nexus |
| signal | Head of Intelligence | Intelligence | vigil |
| scout | World Events Monitor | Intelligence | signal |
| cipher | Market Relevance Analyst | Intelligence | signal |
| lumen | BI Analyst | Intelligence | signal |
| canvas | Head of Design | Design & Reporting | apex |
| flux | Interface Designer | Design & Reporting | canvas |
| prism | Data Visualisation and Report Designer | Design & Reporting | canvas |

---

## Agent Files

Each agent directory contains four files:

| File | Purpose |
|---|---|
| `AGENTS.md` | Identity, chain of command, two-layer memory system, safety considerations |
| `HEARTBEAT.md` | Step-by-step wake cycle checklist the agent runs on every activation |
| `SOUL.md` | Identity, operational posture, voice and tone |
| `TOOLS.md` | Skills, MCP tools, Kanban API reference, role-specific rules |

---

## Key Operating Rules

### Kanban Pipeline
```
INTELLIGENCE QUEUE → DESIGN QUEUE → BACKLOG → TODO → READY → IN PROGRESS → IN REVIEW → QA → DEPLOY → DONE → BLOCKED → CANCELLED
```

### WIP Limits
| Constraint | Limit | Enforced By |
|---|---|---|
| In Progress per developer | 1 | nexus, devcodex, pixel |
| In Review (total) | 4 | nexus |
| Design Queue (total) | 3 | canvas |
| Intelligence Queue dwell time | 2 hours max | signal, vigil |

### Intelligence Pipeline
- **scout** flags every plausible market-moving event to **cipher** immediately using format: `SOURCE / TIMESTAMP / HEADLINE / DESCRIPTION`
- **cipher** scores 1–10 against defined criteria. Score 6+ → lumen. Score 5 → signal decision. Score 4 and below → archived.
- **lumen** researches and produces structured tickets with 7 required fields. Confidence below 60 → flagged to vigil before routing.
- Confirmed intelligence routes to **Design Queue** (UI-relevant) or **Backlog** (logic/data).

### Memory System
Every agent runs a two-layer memory system:
- **muninndb** — queryable facts and decisions (`muninn_remember`, `muninn_recall`, `muninn_guide`)
- **PARA files** — structured file-based memory loaded at cycle start (`./memory/...`)

---

## Chain of Command

All agent names are lowercase. No agent skips a level without explicit apex authorisation.

```
apex → forge → nexus → devcodex / pixel / verdict
apex → vigil → signal → scout / cipher / lumen
apex → canvas → flux / prism
```
