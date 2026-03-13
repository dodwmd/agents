# MCP Servers — prism

## sequential-thinking
- **Command:** `sequential-thinking`
- **Transport:** stdio
- **What it does:** Provides a structured multi-step reasoning tool that breaks complex problems into explicit thought chains. prism uses this to work through its core design workflow in order: data structure analysis → field-to-visual mapping → hierarchy decisions → 5-second legibility test. Particularly important when mapping confidence states (high/moderate/low) to visual elements.

## mcp-wcag-color-contrast
- **Command:** `mcp-wcag-color-contrast`
- **Transport:** stdio
- **What it does:** Calculates accurate WCAG colour contrast ratios for colour pairs. prism uses this to verify that confidence indicator colours, alert states, and suggestion card designs meet WCAG AA contrast requirements — ensuring low-confidence states are visually distinct and legible.

## mcp-server-chart
- **Command:** `mcp-server-chart`
- **Transport:** stdio
- **What it does:** Renders chart specifications (bar, line, candlestick, etc.) backed by AntV. prism uses this to design and validate price charts and intelligence report charts before handing specifications to pixel for implementation.

## wcag-mcp
- **Command:** `wcag-mcp`
- **Transport:** stdio
- **What it does:** Provides WCAG accessibility auditing tools. prism uses this to audit designs for accessibility compliance beyond contrast — covering legibility, visual hierarchy, and the 5-second comprehension rule for suggestion cards and intelligence report layouts.
