# MCP Servers — signal

## mcp-server-time
- **Command:** `mcp-server-time`
- **Transport:** stdio
- **What it does:** Returns the current date and time (local and UTC). signal uses this every wake cycle to calculate ticket dwell times by comparing the current timestamp against Kanban ticket creation times — enforcing the hard 2-hour processing window before escalation.
