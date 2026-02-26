---
name: knowledge-garden::reindex
description: Regenerate the knowledge index from all solution files
allowed-tools:
  - Read
  - Write
  - Glob
  - Grep
  - Bash
---

# Reindex Knowledge Base

Regenerate `docs/knowledge/index.md` by scanning all solution files and extracting their frontmatter into a compact lookup table.

<instructions>

Execute the full knowledge-index skill workflow:

1. Verify `docs/knowledge/solutions/` exists. If not: "Run /knowledge-garden:setup to initialize knowledge-garden first."
2. Glob all solution files in `docs/knowledge/solutions/**/*.md`
3. Read `docs/knowledge/patterns/critical-patterns.md` and extract pattern names + file references
4. For each solution file, read the first 20 lines to extract YAML frontmatter (parallel batches of 10)
5. Write `docs/knowledge/index.md` with the standard index format (header with counts, Critical Patterns section, Solutions table sorted by date descending)
6. Report: solutions indexed, skipped, critical patterns count

See the knowledge-index skill for the full index file format specification.

</instructions>
