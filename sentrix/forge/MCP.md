# MCP Servers — forge

## fetch
- **Command:** `mcp-server-fetch`
- **Transport:** stdio
- **What it does:** Fetches the content of any public URL and returns it as text. forge uses this to read external technical documentation, RFC links, library changelogs, CVE advisories, and framework migration guides when evaluating stack proposals or validating architectural decisions.

## sequential-thinking
- **Command:** `sequential-thinking`
- **Transport:** stdio
- **What it does:** Provides a structured multi-step reasoning tool that breaks complex problems into explicit thought chains. forge uses this for high-stakes architectural decisions made under time pressure (2-hour blocker SLA), ensuring decisions are fully reasoned and documented before being issued as directives.
