---
name: knowledge-garden::knowledge-insights
description: Analyze knowledge base and show patterns, metrics, and trends
allowed-tools:
  - Read
  - Glob
  - Grep
  - Bash
---

# Knowledge Insights

Analyze the knowledge base and surface patterns across documented solutions.

<instructions>

## Execution

1. Verify `docs/knowledge/solutions/` exists. If not, inform the user: "Run /knowledge-garden:setup to initialize knowledge-garden first."
2. Execute the knowledge-insights skill to scan all solution files and aggregate metrics
3. Present the full insights report with tables for problem types, affected modules, root causes, severity distribution, monthly trends, hotspots, and top tags
4. Include actionable recommendations based on the data

</instructions>
