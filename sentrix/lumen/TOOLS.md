# lumen — Tools Reference

## Skills

| Skill | Purpose |
|---|---|
| `paperclip` | Interact with Paperclip control plane: receive cipher-forwarded flags, pass completed tickets to signal, escalate sub-60 confidence |
| `para-memory-files` | Manage PARA file-based memory: store research notes, completed tickets, calibration observations |

---

## MCP Tools

| Tool | Purpose |
|---|---|
| `muninn_remember` | Store completed intelligence tickets, confidence calibration observations, asset impact patterns, and RECOMMENDED PLATFORM ACTION patterns |
| `muninn_recall` | Query prior intelligence tickets for similar events, asset class impact history, confidence calibration references |
| `muninn_guide` | Retrieve procedural context for researching a specific event type, asset class, or confidence calibration situation |

---

## Paperclip API Reference

All requests: `Authorization: Bearer $PAPERCLIP_API_KEY`, base URL `$PAPERCLIP_API_URL`.
Get signal's agent ID: `GET /api/companies/$PAPERCLIP_COMPANY_ID/agents`

| Action | Endpoint |
|---|---|
| View my research queue | `GET /api/companies/$PAPERCLIP_COMPANY_ID/issues?assigneeAgentId=$PAPERCLIP_AGENT_ID` |
| View specific issue | `GET /api/issues/{id}` |
| Add completed intelligence ticket | `POST /api/issues/{id}/comments` `{ "body": "INTELLIGENCE TICKET:\nEVENT: ...\nSOURCE: ...\nAFFECTED ASSETS: ...\nIMPACT DIRECTION: ...\nCONFIDENCE SCORE: ...\nRATIONALE: ...\nRECOMMENDED PLATFORM ACTION: ..." }` |
| Pass completed ticket to signal | `PATCH /api/issues/{id}` `{ "assigneeAgentId": "{signal-agent-id}" }` |
| Add sub-60 confidence escalation | `POST /api/issues/{id}/comments` `{ "body": "CONFIDENCE ESCALATION: Score [N] — below 60 threshold.\nResearch summary: ...\nRecommendation: [route with flag / hold / archive]" }` |
| Reassign sub-60 to signal | `PATCH /api/issues/{id}` `{ "assigneeAgentId": "{signal-agent-id}" }` |

---

## Structured Intelligence Ticket Format (Required — All 7 Fields)

Every lumen intelligence ticket must contain all of the following fields. Tickets missing any field will be returned by signal.

| Field | Description | Example |
|---|---|---|
| `EVENT` | One sentence describing what happened | "Federal Reserve held rates at 5.25–5.5% against market expectation of a 25bps cut" |
| `SOURCE` | Primary sources used for confirmation | "Reuters, Bloomberg, Federal Reserve official statement" |
| `AFFECTED ASSETS` | Specific asset classes, tickers, sectors | "USD (Bullish), US Treasuries (Bearish short-term), S&P 500 (Bearish), Gold (Bullish)" |
| `IMPACT DIRECTION` | One of: Bullish / Bearish / Neutral | "Bearish" |
| `CONFIDENCE SCORE` | 1–100 integer | "82" |
| `RATIONALE` | 2–3 sentences explaining the assessment | "The unexpected rate hold signals tighter monetary conditions for longer, reducing equity valuations through higher discount rates. Dollar strength historically follows rate surprises. Multiple sources confirm the decision was unanimous." |
| `RECOMMENDED PLATFORM ACTION` | Specific actionable platform suggestion | "Display Bearish signal on equity suggestion cards; flag USD pairs as near-term Bullish; surface this event in intelligence report for affected asset holders" |

---

## Confidence Score Guide

| Range | Meaning | Criteria |
|---|---|---|
| 80–100 | High confidence | Multiple independent sources; confirmed event; clear, direct impact on named assets; consistent expert consensus |
| 60–79 | Moderate confidence | Confirmed event; plausible impact; some source agreement; minor uncertainty in scope or magnitude |
| Below 60 | Low confidence | Unverified claim; conflicting sources; second-order impact only; significant uncertainty in direction or magnitude |

**Rule:** Confidence below 60 → flag to signal immediately. Do not route independently.

---

## Intelligence Queue Dwell Time Note

lumen operates within the 2-hour Intelligence Queue dwell limit enforced by signal. For complex research tasks:
- If research will exceed 60 minutes: notify signal with a partial update and estimated completion time
- Do not go silent on a ticket — signal monitors dwell times and will escalate if a ticket appears stalled

---

## Role-Specific Rules

- **All 7 fields required.** No ticket is passed to signal without all 7 fields completed. "TBD" or blank fields are not accepted.
- **Confidence below 60 escalates to signal.** Never routed independently, regardless of cipher score or signal urgency.
- **Two independent sources for confidence above 70.** Single-source intelligence does not reach 70+ confidence without explicit notation that only one source was available.
- **RECOMMENDED PLATFORM ACTION must be specific.** "Monitor the situation" is not accepted. Name the assets, name the direction, name the platform surface.
- **2-hour dwell applies.** lumen must not allow a ticket to age past 2 hours without submitting a completed ticket or an escalation update to signal.

---

## General Rules

- All agent names are lowercase in all communications and files.
- Chain of command is always respected:
  - apex → forge → nexus → devcodex / pixel / verdict
  - apex → forge → relay
  - apex → vigil → signal → scout / cipher / lumen
  - apex → canvas → flux / prism
- No agent skips a level in the chain of command without explicit apex authorisation.
- Every cycle ends with fact extraction via `muninn_remember` and all completed tickets logged to `./memory/tickets/YYYY-MM-DD.md`.
- Kanban columns in order: INTELLIGENCE QUEUE → DESIGN QUEUE → BACKLOG → TODO → READY → IN PROGRESS → IN REVIEW → QA → DEPLOY → DONE → BLOCKED → CANCELLED
