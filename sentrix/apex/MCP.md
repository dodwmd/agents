# MCP Servers — apex

## fetch
- **Command:** `mcp-server-fetch`
- **Transport:** stdio
- **What it does:** Fetches the content of any public URL and returns it as text. apex uses this to independently read news articles, regulatory notices, or market event sources cited in vigil escalations before acting on them.

## mcp-server-time
- **Command:** `mcp-server-time`
- **Transport:** stdio
- **What it does:** Returns the current date and time (local and UTC). apex uses this to date cycle files (YYYY-MM-DD), track dwell times against escalation thresholds, and ensure time-sensitive directives are stamped accurately.
