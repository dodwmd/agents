# MCP Servers — pixel

## fetch
- **Command:** `mcp-server-fetch`
- **Transport:** stdio
- **What it does:** Fetches the content of any public URL and returns it as text. pixel uses this to inspect API endpoint responses and payload shapes during frontend implementation and data pipeline integration testing.

## playwright-mcp
- **Command:** `playwright-mcp`
- **Transport:** stdio
- **What it does:** Drives a headless browser to render pages, capture screenshots, and interact with UI elements. pixel uses this to verify that dashboard components and UI surfaces match flux/prism design assets exactly — enforcing pixel-perfect fidelity through rendered output inspection and visual regression checking.

## context7
- **Command:** `context7`
- **Transport:** stdio
- **What it does:** Provides up-to-date, version-accurate library and framework documentation. pixel uses this to look up current API signatures, component props, and integration patterns for the frontend frameworks, charting libraries, and pipeline tooling in use — avoiding stale or hallucinated documentation during implementation.
