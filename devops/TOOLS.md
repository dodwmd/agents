# Tools Reference — DevOps Engineer

External skills available to the DevOps Engineer.

---

## External Skills

### `paperclip`
All organizational coordination: ticket checkout, status updates, comments, and escalation. Every deploy action — pipeline trigger, smoke test result, rollback, handoff back to QA — is recorded via a Paperclip comment. Required before any deploy action.

### `muninndb` (MCP)
Episodic memory for deploy history. Store deploy outcomes (service, version, result, signal summary) immediately after each run. Recall before deploying a service to surface its recent deploy history and known fragile areas.

Key operations:
- `muninn_remember` — store a deploy outcome, rollback decision, or incident
- `muninn_recall` — surface deploy history for a service before triggering the pipeline
- `muninn_guide` — learn available operations if uncertain

### `para-memory-files`
Structured file-based memory for deploy logs. Conventions for writing post-deploy records to `$AGENT_HOME/memory/deploys/`. Use alongside muninndb for structured operational memory. See `AGENTS.md` for the two-layer memory split.
