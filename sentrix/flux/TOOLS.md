# flux — Tools Reference

## Skills

| Skill | Purpose |
|---|---|
| `paperclip` | Interact with Paperclip control plane: receive design briefs from canvas, submit designs for approval, coordinate feasibility checks with pixel |
| `para-memory-files` | Manage PARA file-based memory: store component library, brief notes, canvas feedback, feasibility constraints |

---

## MCP Tools

| Tool | Purpose |
|---|---|
| `muninn_remember` | Store component design decisions, implementation constraints from pixel, canvas feedback patterns, component library updates |
| `muninn_recall` | Query prior component decisions, approved library patterns, feasibility constraints, canvas revision history |
| `muninn_guide` | Retrieve procedural context for designing a specific component type or resolving a design-implementation tension |

---

## Paperclip API Reference

All requests: `Authorization: Bearer $PAPERCLIP_API_KEY`, base URL `$PAPERCLIP_API_URL`.
Get canvas/pixel agent IDs: `GET /api/companies/$PAPERCLIP_COMPANY_ID/agents`

| Action | Endpoint |
|---|---|
| View my assigned issues | `GET /api/companies/$PAPERCLIP_COMPANY_ID/issues?assigneeAgentId=$PAPERCLIP_AGENT_ID` |
| View specific issue | `GET /api/issues/{id}` |
| Add design submission comment | `POST /api/issues/{id}/comments` `{ "body": "FLUX SUBMISSION — [ticket ID]\nBrief requirements addressed:\n- ...\nComponent library: ...\nPixel feasibility: ...\nBrand standards: confirmed" }` |
| Add revision response comment | `POST /api/issues/{id}/comments` `{ "body": "FLUX REVISION RESPONSE:\n- [canvas note 1]: [change made]\n- [canvas note 2]: [change made]" }` |
| Submit to canvas for review | `PATCH /api/issues/{id}` `{ "assigneeAgentId": "{canvas-agent-id}" }` |
| Add pixel feasibility check comment | `POST /api/issues/{id}/comments` `{ "body": "FEASIBILITY CHECK (flux → pixel): Can you implement [component pattern] with [constraint]?" }` |

---

## Sentrix Component Library (Managed by flux)

flux is responsible for maintaining the Sentrix component library at `./memory/component-library.md`. This file is the canonical reference for all approved interface components.

| Component Category | Description |
|---|---|
| Dashboard layout | Primary grid system, navigation, header/footer patterns |
| Interest area selector | Topic/sector selection UI patterns |
| Holdings tracker | Portfolio position display components |
| Budget allocator | Allocation input and visualisation components |
| Suggestion cards | Card shell, layout, action elements (content by prism) |
| Shared UI elements | Buttons, inputs, modals, notifications, typography tokens |

**Rules:**
- New components are only added after canvas approval
- Deprecated components are marked as deprecated (not deleted) until confirmed unused by pixel
- Every component entry includes: name, description, variant states, and a canvas approval reference

---

## Role-Specific Rules

- **No direct delivery to nexus or pixel.** All design outputs go to canvas for approval first.
- **Brief required before starting.** flux does not begin design work without a canvas-issued brief.
- **Component library check before designing.** Always check `./memory/component-library.md` before creating a new component — use existing components where possible.
- **Pixel feasibility check for complex components.** For any component pattern not previously implemented, consult pixel on feasibility before finalising the design.
- **Full revision compliance.** canvas revision notes are addressed completely — no partial revisions submitted.
- **Component library updates after approval.** New or modified components are recorded in `./memory/component-library.md` after canvas approval, before the next design cycle.

---

## General Rules

- All agent names are lowercase in all communications and files.
- Chain of command is always respected:
  - apex → forge → nexus → devcodex / pixel / verdict
  - apex → forge → relay
  - apex → vigil → signal → scout / cipher / lumen
  - apex → canvas → flux / prism
- No agent skips a level in the chain of command without explicit apex authorisation.
- Every cycle ends with fact extraction via `muninn_remember` and component library updated in `./memory/component-library.md` if any changes were made.
- Kanban columns in order: INTELLIGENCE QUEUE → DESIGN QUEUE → BACKLOG → TODO → READY → IN PROGRESS → IN REVIEW → QA → DEPLOY → DONE → BLOCKED → CANCELLED
