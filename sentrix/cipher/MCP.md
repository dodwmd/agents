# MCP Servers — cipher

## fetch
- **Command:** `mcp-server-fetch`
- **Transport:** stdio
- **What it does:** Fetches the content of any public URL and returns it as text. cipher uses this to verify source credibility when a scout flag cites an unfamiliar or ambiguous outlet — fetching the source URL to confirm it is a legitimate publication (e.g. central bank press release vs. rumour blog) before placing a score within its range.
