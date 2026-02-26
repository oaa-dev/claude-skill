---
name: knowledge-garden::brainstorm
description: Explore requirements and approaches with knowledge-backed context
argument-hint: "[topic or feature to brainstorm]"
allowed-tools:
  - Read
  - Write
  - Bash
  - Glob
  - Grep
  - Task
  - AskUserQuestion
  - Edit
  - TodoWrite
---

# Knowledge-Backed Brainstorm

Explore requirements and approaches for a topic, informed by institutional knowledge from the knowledge garden.

<instructions>

## Phase 1: Knowledge Auto-Read

Before brainstorming, surface relevant institutional knowledge using the pre-computed index.

1. **Check for knowledge index.** Try to read `docs/knowledge/index.md`.
   - **If not found:** check if `docs/knowledge/solutions/` exists.
     - If no solutions dir: "Run /knowledge-garden:setup to initialize knowledge-garden first." STOP.
     - If solutions dir exists but no index: "Run /knowledge-garden::reindex to generate the index." Fall back to legacy grep search (Step 1b).
   - **If found:** proceed to Step 2.

   <details><summary>Step 1b: Legacy grep fallback (only if no index)</summary>

   Run these Grep calls in parallel:
   ```
   Grep: pattern="title:.*[keyword]" path=docs/knowledge/solutions/ output_mode=files_with_matches -i=true
   Grep: pattern="tags:.*([keyword1]|[keyword2])" path=docs/knowledge/solutions/ output_mode=files_with_matches -i=true
   Grep: pattern="module:.*[keyword]" path=docs/knowledge/solutions/ output_mode=files_with_matches -i=true
   ```
   Read `docs/knowledge/patterns/critical-patterns.md`. Skip to Step 5.
   </details>

2. **Check if empty.** If the index header contains `<!-- Solutions: 0 -->`, state: "Knowledge base is empty. Skipping knowledge context." Proceed directly to Phase 2.

3. **Scan index in-context.** Extract keywords from `$ARGUMENTS` and scan the Solutions table and Critical Patterns section for matches. No tool calls needed -- the index is already in context.

4. **Deep-read strong matches.** For rows where Module, Type, Tags, or Critical Patterns strongly match the topic (typically 0-3 files), read the full solution file for detailed context.

5. **Present knowledge context:**

   ```
   ## Knowledge Context

   **Relevant learnings found:** X documents
   - [Title]: [key insight] (severity)
   - ...

   **Critical patterns to consider:**
   - [pattern summary]
   ```

   If no relevant learnings found, state: "No existing learnings found for this topic. Fresh territory."

## Phase 2: Collaborative Brainstorm

With knowledge context established, guide exploration of the topic from `$ARGUMENTS`.

1. **Frame the topic.** Summarize what is being brainstormed and any constraints from the knowledge context.

2. **Explore dimensions.** Walk through these areas with the user:
   - **Problem/Goal**: What exactly needs to be solved or built?
   - **Users/Stakeholders**: Who is affected?
   - **Approaches**: What are the possible ways to tackle this? (Present 2-4 options)
   - **Tradeoffs**: What are the pros/cons of each approach?
   - **Known risks**: What does the knowledge base tell us about pitfalls in this area?
   - **Open questions**: What do we still need to figure out?

3. **Iterate with user.** Use AskUserQuestion to get the user's preferences on approaches and tradeoffs. Continue refining until the brainstorm feels complete.

## Phase 3: Save Brainstorm

1. **Create brainstorm directory** if it doesn't exist:
   ```bash
   mkdir -p docs/brainstorms
   ```

2. **Generate filename**: `YYYY-MM-DD-<sanitized-topic>.md` where the topic is derived from `$ARGUMENTS`.

3. **Write brainstorm file** with this structure:

   ```markdown
   # Brainstorm: [Topic]

   **Date:** YYYY-MM-DD
   **Status:** Draft

   ## Knowledge Context

   [Summary of relevant learnings surfaced in Phase 1]

   ## Problem / Goal

   [What we're trying to solve]

   ## Approaches Considered

   ### Approach A: [Name]
   - **Description:** ...
   - **Pros:** ...
   - **Cons:** ...

   ### Approach B: [Name]
   - **Description:** ...
   - **Pros:** ...
   - **Cons:** ...

   ## Decision

   [Preferred approach and rationale, or "TBD" if not yet decided]

   ## Open Questions

   - [Unresolved questions]

   ## Next Steps

   - [ ] [Action items]
   ```

4. **Confirm output.** Tell the user where the brainstorm was saved and suggest next steps:
   - `/plan` to create an implementation plan from this brainstorm
   - Continue refining the brainstorm

## Arguments

- `$ARGUMENTS` (required): topic or feature description to brainstorm about.

</instructions>
