---
name: knowledge-garden::review
description: Review code against documented patterns and known issues
argument-hint: "[file path, branch name, or PR number]"
allowed-tools:
  - Read
  - Bash
  - Glob
  - Grep
  - Task
  - AskUserQuestion
---

# Knowledge-Backed Code Review

Review code changes against institutional knowledge, documented patterns, and known issues.

<instructions>

## Phase 1: Knowledge Auto-Read

Before reviewing, load relevant institutional knowledge using the pre-computed index.

1. **Check for knowledge index.** Try to read `docs/knowledge/index.md`.
   - **If not found:** check if `docs/knowledge/solutions/` exists.
     - If no solutions dir: "Run /knowledge-garden:setup to initialize knowledge-garden first." STOP.
     - If solutions dir exists but no index: "Run /knowledge-garden::reindex to generate the index." Fall back to legacy grep search (Step 1b).
   - **If found:** proceed to Step 2.

   <details><summary>Step 1b: Legacy grep fallback (only if no index)</summary>

   Run these Grep calls in parallel:
   ```
   Grep: pattern="module:.*([Module1]|[Module2])" path=docs/knowledge/solutions/ output_mode=files_with_matches -i=true
   Grep: pattern="component:.*([component1]|[component2])" path=docs/knowledge/solutions/ output_mode=files_with_matches -i=true
   Grep: pattern="tags:.*([keyword1]|[keyword2])" path=docs/knowledge/solutions/ output_mode=files_with_matches -i=true
   ```
   Read `docs/knowledge/patterns/critical-patterns.md`. Skip to Step 6.
   </details>

2. **Check if empty.** If the index header contains `<!-- Solutions: 0 -->`, state: "Knowledge base is empty. Skipping knowledge context." Proceed directly to Phase 2.

3. **Determine review scope.** Based on `$ARGUMENTS`:
   - If a file path: review that file's recent changes
   - If a branch name: review all changes on that branch vs the default branch
   - If a PR number: use `gh pr diff $ARGUMENTS` to get the diff
   - If empty: review staged/unstaged changes (`git diff` and `git diff --cached`)

4. **Identify affected modules.** From the diff, extract:
   - Module names (from file paths and namespaces)
   - Component types (models, controllers, services, pages, hooks, etc.)
   - Technical patterns in use (queries, caching, auth, validation, etc.)

5. **Scan index in-context.** Using the identified modules and components, scan the Solutions table and Critical Patterns section for matches. No tool calls needed -- the index is already in context.

6. **Deep-read strong matches.** For rows where Module, Component, or Tags strongly match the affected areas (typically 0-3 files), read the full solution file to understand root causes and solutions.

## Phase 2: Knowledge-Informed Review

With institutional knowledge loaded, perform the review.

1. **Cross-reference changes against known issues.** For each changed file/module:
   - Check if any documented root causes apply to the changes
   - Check if documented symptoms could be introduced by the changes
   - Check if critical patterns are being followed or violated

2. **Review categories.** Evaluate the changes across these dimensions:

   **Knowledge-backed findings** (from the knowledge base):
   - Does this change repeat a documented mistake?
   - Does it touch an area with known critical patterns?
   - Does it handle edge cases that past solutions identified?

   **General code quality:**
   - Correctness: Does the code do what it intends?
   - Patterns: Does it follow the project's established patterns?
   - Edge cases: Are boundary conditions handled?
   - Performance: Any obvious performance concerns?

3. **Severity classification.** Rate each finding:
   - **Critical**: Repeats a documented root cause or violates a critical pattern
   - **Warning**: Touches an area with past issues but doesn't clearly repeat them
   - **Suggestion**: General improvement not tied to documented knowledge

## Phase 3: Report

Present the review in this structure:

```
## Knowledge-Backed Code Review

### Review Scope
- **Files reviewed:** X files
- **Modules affected:** [list]
- **Knowledge matches:** Y relevant learnings consulted

### Critical Findings
[Findings backed by documented issues -- highest priority]

#### 1. [Finding title]
- **File:** `path/to/file.ext:line`
- **Knowledge ref:** [docs/knowledge/solutions/category/filename.md]
- **Issue:** [What the documented learning says about this pattern]
- **Recommendation:** [What should change]

### Warnings
[Findings in areas with past issues]

### Suggestions
[General improvements]

### Knowledge Gaps
[Areas touched by changes that have no documented knowledge -- potential blind spots]

### Summary
- Critical: X findings
- Warnings: Y findings
- Suggestions: Z findings
- Verdict: [Approve / Request changes / Needs discussion]
```

## Arguments

- `$ARGUMENTS` (optional): file path, branch name, or PR number to review. If omitted, reviews current staged/unstaged changes.

</instructions>
