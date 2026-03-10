# Tools Reference — DevOps Engineer

External skills available to the DevOps Engineer.

---

## External Skills

### `paperclip`
**Documentation skill only — it loads reference instructions, it does not execute API calls.**

Invoke the `paperclip` skill once at the start of a heartbeat to load the API reference into context. After that, make every actual API call yourself using `Bash` + `curl` against `$PAPERCLIP_API_URL`. Do not pass arguments like `get-task <id>` to the skill — those are not valid commands.

Covers all organizational coordination: ticket checkout, status updates, comments, and escalation. Every deploy action — pipeline trigger, smoke test result, rollback, handoff back to QA — is recorded via `curl` calls to the Paperclip API.

### `muninndb` (MCP)
Episodic memory for deploy history. Store deploy outcomes (service, version, result, signal summary) immediately after each run. Recall before deploying a service to surface its recent deploy history and known fragile areas.

Key operations:
- `muninn_remember` — store a deploy outcome, rollback decision, or incident
- `muninn_recall` — surface deploy history for a service before triggering the pipeline
- `muninn_guide` — learn available operations if uncertain

### `para-memory-files`
Structured file-based memory for deploy logs. Conventions for writing post-deploy records to `$AGENT_HOME/memory/deploys/`. Use alongside muninndb for structured operational memory. See `AGENTS.md` for the two-layer memory split.
