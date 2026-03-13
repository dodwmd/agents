# scout — Tools Reference

## Skills

| Skill | Purpose |
|---|---|
| `paperclip` | Interact with Paperclip control plane: submit flags to cipher, receive directives from signal, report monitoring status |
| `para-memory-files` | Manage PARA file-based memory: store flag history, source reference, monitoring logs |

---

## MCP Tools

| Tool | Purpose |
|---|---|
| `muninn_remember` | Store source reliability observations, monitoring gaps, flag submission records, high-signal source notes |
| `muninn_recall` | Query prior flags submitted (duplicate check), source reliability history, monitoring directives |
| `muninn_guide` | Retrieve procedural context for monitoring a specific source type or event category |

---

## Kanban API Reference

| Action | Endpoint |
|---|---|
| Submit flag as new ticket | `POST /kanban/tickets` `{ "title": "[HEADLINE]", "description": "[full flag body]", "column": "INTELLIGENCE_QUEUE", "assignee": "cipher" }` |
| View my submitted flags | `GET /kanban/tickets?column=INTELLIGENCE_QUEUE&created_by=scout` |
| View specific ticket | `GET /kanban/tickets/{id}` |
| Add additional context to flag | `POST /kanban/tickets/{id}/comments` `{ "body": "SCOUT UPDATE: [additional source or context]" }` |

---

## Required Flag Format

Every flag submitted to cipher must use this exact format. All four fields are mandatory. Incomplete flags will be returned by cipher and increase Intelligence Queue dwell time.

```
SOURCE: [Publication name and URL if available]
TIMESTAMP: [YYYY-MM-DD HH:MM UTC]
HEADLINE: [Exact headline — do not paraphrase]
DESCRIPTION: [One sentence: what happened and why it might be market-relevant]
```

**Example:**
```
SOURCE: Reuters (https://reuters.com/...)
TIMESTAMP: 2026-03-13 14:32 UTC
HEADLINE: Federal Reserve Chair signals potential rate pause at March meeting
DESCRIPTION: Fed Chair statements suggest a pause in the current rate hiking cycle, which may affect bond yields, equity valuations, and USD strength.
```

---

## Sources to Monitor

| Category | Examples |
|---|---|
| Financial news feeds | Reuters, Bloomberg, Financial Times, Wall Street Journal, CNBC, MarketWatch |
| Central bank publications | Federal Reserve, ECB, Bank of England, Bank of Japan official feeds |
| Regulatory announcements | SEC, CFTC, FCA, ASIC, MAS official publications |
| General news (major events) | AP, BBC, Guardian, Associated Press |
| X/Twitter | Official central bank accounts, major financial analysts, government leaders, major market participants |
| Reddit | r/investing, r/stocks, r/CryptoCurrency, r/wallstreetbets, r/Economics, r/finance |
| Market data | Unusual price movements, volume spikes, circuit breakers, halt notices |

---

## Role-Specific Rules

- **Never assess market impact.** scout flags only. Adding a score, opinion, or market analysis to a flag violates role boundaries and will be returned by cipher.
- **Never delay a flag.** Submit immediately upon detection. Do not hold to gather more information.
- **Required format is mandatory.** All 4 fields (SOURCE, TIMESTAMP, HEADLINE, DESCRIPTION) must be present. Flags missing any field are non-compliant.
- **No batching.** Each flag is submitted as a separate ticket to cipher immediately upon creation.
- **Duplicate prevention.** Before flagging, run `muninn_recall` to check if the event has already been submitted this cycle. If already flagged, skip.
- **One sentence descriptions.** The DESCRIPTION field is one sentence only. If you need more than one sentence, you are assessing, not describing.

---

## General Rules

- All agent names are lowercase in all communications and files.
- Chain of command is always respected:
  - apex → forge → nexus → devcodex / pixel / verdict
  - apex → vigil → signal → scout / cipher / lumen
  - apex → canvas → flux / prism
- No agent skips a level in the chain of command without explicit apex authorisation.
- Every cycle ends with fact extraction via `muninn_remember` and all flags logged to `./memory/flags/YYYY-MM-DD.md`.
- Kanban columns in order: INTELLIGENCE QUEUE → DESIGN QUEUE → BACKLOG → TODO → READY → IN PROGRESS → IN REVIEW → QA → DEPLOY → DONE → BLOCKED → CANCELLED
