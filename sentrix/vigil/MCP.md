# MCP Servers — vigil

## fetch
- **Command:** `mcp-server-fetch`
- **Transport:** stdio
- **What it does:** Fetches the content of any public URL and returns it as text. vigil uses this to verify intelligence sources by fetching the actual web content cited in lumen tickets — source verification is a core validation step before routing to apex.

## duckduckgo-mcp-server
- **Command:** `duckduckgo-mcp-server`
- **Transport:** stdio
- **What it does:** Performs keyless web searches via DuckDuckGo. vigil uses this to cross-reference and corroborate market-moving events cited in intelligence tickets, providing independent source confirmation before approving routing decisions.

## mcp-hnews
- **Command:** `hn-mcp`
- **Transport:** stdio
- **What it does:** Provides read access to Hacker News (free, keyless). vigil uses this to corroborate tech-adjacent regulatory and crypto-finance market events — HN surfaces early signals for technology policy, exchange incidents, and infrastructure events that affect the intelligence queue.

## yfinance-mcp
- **Command:** `yfinance-mcp`
- **Transport:** stdio
- **What it does:** Retrieves equity, index, and asset price data via Yahoo Finance (free, no API key required). vigil uses this to validate the IMPACT DIRECTION and AFFECTED ASSETS fields on non-crypto intelligence tickets by checking real market data before approving routing decisions.

## coingecko
- **URL:** `https://mcp.api.coingecko.com/mcp`
- **Transport:** HTTP (remote)
- **What it does:** Provides real-time cryptocurrency market data (prices, market cap, volume) for 15,000+ coins across 1,000+ exchanges via the official CoinGecko MCP server. No API key required for the public tier. vigil uses this to validate IMPACT DIRECTION and AFFECTED ASSETS fields on crypto-finance intelligence tickets.
