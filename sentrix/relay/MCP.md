# MCP Servers — relay

## fetch
- **Command:** `mcp-server-fetch`
- **Transport:** stdio
- **What it does:** Fetches the content of any public URL and returns it as text. relay uses this to query deployment pipeline status endpoints, health check URLs, monitoring dashboards, and CI/CD run APIs to verify deployment outcomes and environment health.

## desktop-commander
- **Command:** `desktop-commander`
- **Transport:** stdio
- **What it does:** Executes shell commands and returns their output. relay uses this to trigger CI/CD pipelines, run deployment scripts, execute infrastructure commands, perform smoke tests against deployed endpoints, inspect logs, and verify environment state post-deployment.
