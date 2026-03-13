# MCP Servers — canvas

## fetch
- **Command:** `mcp-server-fetch`
- **Transport:** stdio
- **What it does:** Fetches the content of any public URL and returns it as text. canvas uses this to retrieve external design references, WCAG documentation, component library specs, and accessibility guidelines when writing briefs for flux and prism or verifying implementation feasibility for pixel.

## puppeteer
- **Command:** `playwright-mcp`
- **Transport:** stdio
- **What it does:** Drives a headless browser to render pages and components. canvas uses this to visually verify design assets and run WCAG AA colour contrast checks against rendered components — directly relevant to the explicit contrast requirement in the revision workflow.
