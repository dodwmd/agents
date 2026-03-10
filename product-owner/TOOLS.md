# Tools Reference — Product Owner

External skills available to the Product Owner.

---

## External Skills

### `paperclip`
**Documentation skill only — it loads reference instructions, it does not execute API calls.**

Invoke the `paperclip` skill at the start of a heartbeat to load the API reference into context. After that, make every actual API call yourself using `Bash` + `curl` against `$PAPERCLIP_API_URL`. Do not pass arguments like `get-task <id>` or `get-comment <id>` to the skill — those are not valid commands and the skill will just return documentation.

Correct usage pattern:
1. Invoke skill once to load reference: `Skill("paperclip")`
2. Then use `Bash` to call the API directly:
   ```bash
   curl -s "$PAPERCLIP_API_URL/api/issues/$PAPERCLIP_TASK_ID" \
     -H "Authorization: Bearer $PAPERCLIP_API_KEY"
   ```

All organizational coordination — issue creation, status updates, comments, checkout, and cancellations — is done via `curl` calls to the Paperclip API. The skill documents the endpoints; `Bash` executes them.

### `muninndb` (MCP)
Episodic and semantic memory. Used to recall product decisions, stakeholder context, and patterns in scope changes or cancellations. Store intake events, cancellation reasons, and Done review outcomes immediately when they occur.

Key operations:
- `muninn_remember` — store a decision, stakeholder request, or pattern
- `muninn_recall` — surface context before writing a ticket, reviewing Done, or handling a cancellation
- `muninn_guide` — learn available operations if uncertain

### `para-memory-files`
Defines PARA folder structure and atomic fact schema conventions for structured file-based memory: daily plans, product decision logs, cancellation logs. Use alongside muninndb for structured operational memory. See `AGENTS.md` for the two-layer memory split.
