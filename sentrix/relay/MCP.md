# MCP Servers — relay

## fetch
- **Command:** `mcp-server-fetch`
- **Transport:** stdio
- **What it does:** Fetches the content of any public URL and returns it as text. relay uses this to query deployment pipeline status endpoints, health check URLs, monitoring dashboards, and CI/CD run APIs to verify deployment outcomes and environment health.

## desktop-commander
- **Command:** `desktop-commander`
- **Transport:** stdio
- **What it does:** Executes shell commands and returns their output. relay uses this to trigger CI/CD pipelines, run deployment scripts, execute infrastructure commands, perform smoke tests against deployed endpoints, inspect logs, and verify environment state post-deployment.

## time
- **Command:** `mcp-server-time`
- **Transport:** stdio
- **What it does:** Returns the current time and performs timezone conversions. relay uses this to generate accurate ISO timestamps for deployment notes, rollback reports, and incident records — every deployment log format requires a precise timestamp.

## postgres
- **Command:** `mcp-server-postgres`
- **Transport:** stdio
- **What it does:** Queries and inspects PostgreSQL databases. relay uses this to verify post-deployment database state: confirm migrations applied correctly, check schema integrity, and validate data consistency as part of the production health verification window.

## playwright
- **Command:** `playwright-mcp`
- **Transport:** stdio
- **What it does:** Browser automation via Chromium. relay uses this to run browser-based smoke tests against deployed web app endpoints during the post-deployment observation window — verifying that critical user-facing flows are functional before marking a ticket DONE.

## sequential-thinking
- **Command:** `sequential-thinking-mcp`
- **Transport:** stdio
- **What it does:** Structures complex reasoning through dynamic thought chains. relay uses this for deployment risk assessment (evaluating dependencies, environment state, recent incidents, and rollback readiness) and for methodical diagnosis when a deployment anomaly requires a rollback decision.
