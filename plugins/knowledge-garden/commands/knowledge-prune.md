---
name: knowledge-garden::knowledge-prune
description: Flag stale learnings for cleanup, archiving, or deletion
allowed-tools:
  - Read
  - Write
  - Bash
  - Glob
  - Grep
  - AskUserQuestion
---

# Knowledge Prune

Flag stale or outdated learnings for cleanup.

<instructions>

## Execution

1. Verify `docs/knowledge/solutions/` exists. If not, inform the user: "Run /knowledge-garden:setup to initialize knowledge-garden first."
2. If `$ARGUMENTS` is provided, treat it as a custom staleness threshold (default: 50). Example: `/knowledge-prune 30` uses 30 as the threshold.
3. Execute the knowledge-prune skill to score all solution files and present candidates
4. Guide the user through the interactive selection and action process

## Arguments

- `$ARGUMENTS` (optional): staleness score threshold (0-100). Default: 50. Lower values flag more documents.

</instructions>
