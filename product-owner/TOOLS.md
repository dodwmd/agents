# Tools Reference — Product Owner

External skills available to the Product Owner.

---

## External Skills

### `paperclip`
All organizational coordination: issue creation, status updates, comments, checkout, and cancellations. Every Backlog ticket is created via Paperclip. Every acceptance criteria review comment and cancellation is recorded via Paperclip. Required before any coordination action.

### `muninndb` (MCP)
Episodic and semantic memory. Used to recall product decisions, stakeholder context, and patterns in scope changes or cancellations. Store intake events, cancellation reasons, and Done review outcomes immediately when they occur.

Key operations:
- `muninn_remember` — store a decision, stakeholder request, or pattern
- `muninn_recall` — surface context before writing a ticket, reviewing Done, or handling a cancellation
- `muninn_guide` — learn available operations if uncertain

### `para-memory-files`
Defines PARA folder structure and atomic fact schema conventions for structured file-based memory: daily plans, product decision logs, cancellation logs. Use alongside muninndb for structured operational memory. See `AGENTS.md` for the two-layer memory split.
