# MCP Servers — verdict

## fetch
- **Command:** `mcp-server-fetch`
- **Transport:** stdio
- **What it does:** Fetches the content of any public URL and returns it as text. verdict uses this to make HTTP requests against running application endpoints and API routes, verifying that functional acceptance criteria (API responses, data pipeline outputs, UI endpoints) behave as specified.

## desktop-commander
- **Command:** `desktop-commander`
- **Transport:** stdio
- **What it does:** Executes shell commands and returns their output. verdict uses this to run the test suite against submissions from devcodex and pixel — verifying that developer-written tests exist, pass, and cover the acceptance criteria. Without this, test coverage checking would be theoretical only.
