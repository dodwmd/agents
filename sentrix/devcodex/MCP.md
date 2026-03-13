# MCP Servers — devcodex

## fetch
- **Command:** `mcp-server-fetch`
- **Transport:** stdio
- **What it does:** Fetches the content of any public URL and returns it as text. devcodex uses this to inspect external API endpoints, read integration documentation, and validate HTTP responses when implementing and testing backend service integrations.

## mcp-server-sqlite
- **Command:** `mcp-server-sqlite`
- **Transport:** stdio
- **What it does:** Provides direct read/write access to SQLite database files. devcodex uses this to inspect, query, and validate local database state when implementing and testing the data layer.

## mcp-server-postgres
- **Command:** `mcp-server-postgres`
- **Transport:** stdio
- **What it does:** Provides direct read/write access to a PostgreSQL database. devcodex uses this to interact with the production-pattern relational database during backend development and data layer validation.
