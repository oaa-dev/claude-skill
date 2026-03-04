---
name: knowledge-garden::quickfix
description: Fix a minor issue fast. No planning or brainstorming -- just fix it.
allowed-tools:
  - Read
  - Write
  - Edit
  - Bash
  - Glob
  - Grep
  - Agent
---

# Quickfix

Fix a minor issue using the quickfix skill.

<instructions>

## Execution

1. Execute the quickfix skill with `$ARGUMENTS` as the issue description
2. If `$ARGUMENTS` is empty, check recent conversation context for the issue to fix
3. Go straight to fixing -- no planning, no brainstorming, no knowledge lookup

</instructions>
