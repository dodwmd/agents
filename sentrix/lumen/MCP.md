# MCP Servers — lumen

## fetch
- **Command:** `mcp-server-fetch`
- **Transport:** stdio
- **What it does:** Fetches the content of any public URL and returns it as text. lumen uses this to retrieve full article and page content from URLs discovered during research — reading primary sources and confirming event details before assigning confidence scores.

## duckduckgo-mcp-server
- **Command:** `duckduckgo-mcp-server`
- **Transport:** stdio
- **What it does:** Performs keyless web searches via DuckDuckGo. lumen uses this to locate at least two independent sources per event — a hard requirement of the confidence scoring protocol before producing an intelligence ticket for signal.
