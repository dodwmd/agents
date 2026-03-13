# MCP Servers — scout

## fetch
- **Command:** `mcp-server-fetch`
- **Transport:** stdio
- **What it does:** Fetches the content of any public URL and returns it as text. scout uses this as its primary tool for scraping news articles, central bank publications, regulatory announcements, and financial wire pages by direct URL.

## duckduckgo-mcp-server
- **Command:** `duckduckgo-mcp-server`
- **Transport:** stdio
- **What it does:** Performs keyless web searches via DuckDuckGo. scout uses this for broad real-time news sweeps when no specific URL is known — catching breaking events across multiple outlets simultaneously.

## reddit-mcp
- **Command:** `mcp-reddit`
- **Transport:** stdio
- **What it does:** Provides read access to Reddit using the public JSON API (no key required). scout monitors subreddits including r/investing, r/stocks, r/CryptoCurrency, r/wallstreetbets, r/Economics, and r/finance for early signals and community-sourced market events.

## twitter-mcp
- **Command:** `twitter-mcp`
- **Transport:** stdio
- **What it does:** Reads public X/Twitter feeds. scout uses this to monitor official central bank accounts, government leaders, and major market participants for real-time announcements that may constitute market-moving events.
