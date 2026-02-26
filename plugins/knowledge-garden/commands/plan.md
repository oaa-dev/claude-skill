---
name: knowledge-garden::plan
description: Create implementation plans informed by past learnings and known gotchas
argument-hint: "[feature description, brainstorm file, or specification]"
allowed-tools:
  - Read
  - Write
  - Bash
  - Glob
  - Grep
  - Task
  - AskUserQuestion
  - Edit
  - EnterPlanMode
  - TodoWrite
---

# Knowledge-Backed Implementation Plan

Create an implementation plan that leverages institutional knowledge to avoid known pitfalls and apply proven patterns.

<instructions>

## Phase 1: Knowledge Auto-Read

Before planning, surface all relevant institutional knowledge using the pre-computed index.

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
   Grep: pattern="module:.*[module]" path=docs/knowledge/solutions/ output_mode=files_with_matches -i=true
   Grep: pattern="symptoms:.*[symptom]" path=docs/knowledge/solutions/ output_mode=files_with_matches -i=true
   ```
   Read `docs/knowledge/patterns/critical-patterns.md`. Skip to Step 5.
   </details>

2. **Check if empty.** If the index header contains `<!-- Solutions: 0 -->`, state: "Knowledge base is empty. Skipping knowledge context." Proceed directly to Phase 2.

3. **Determine input.** If `$ARGUMENTS` points to a file (brainstorm, spec, or doc), read it fully. Otherwise treat `$ARGUMENTS` as the feature description.

4. **Scan index in-context.** Extract keywords (module names, technical terms, problem indicators) from the input and scan the Solutions table and Critical Patterns section for matches. No tool calls needed -- the index is already in context.

5. **Deep-read strong matches.** For rows where Module, Type, Tags, or Critical Patterns strongly match (typically 0-3 files), read the full solution file to extract solutions and prevention guidance.

6. **Present knowledge context:**

   ```
   ## Knowledge Context for Planning

   **Relevant learnings:** X documents
   - [Title]: [key insight] (severity: [level])
   - ...

   **Known gotchas in affected modules:**
   - [gotcha from past solution]
   - ...

   **Critical patterns to follow:**
   - [pattern from critical-patterns.md]
   ```

## Phase 2: Create Plan

With knowledge context established, create a structured implementation plan.

1. **Analyze scope.** Break down the feature/task into concrete implementation steps. Consider:
   - Which files need to change?
   - What order should changes happen in?
   - What are the dependencies between steps?

2. **Apply knowledge guardrails.** For each step, cross-reference against learnings:
   - If a step touches a module with documented issues, add a warning note
   - If a step involves a pattern that has a documented gotcha, reference it
   - If critical patterns apply, ensure the plan follows them

3. **Flag risks.** Based on knowledge base findings, explicitly call out:
   - Steps that touch areas with past critical/high-severity issues
   - Patterns that previously caused problems
   - Areas with no documented knowledge (blind spots)

4. **Get user input.** Use AskUserQuestion if there are ambiguous requirements or multiple valid approaches.

## Phase 3: Save Plan

1. **Create plans directory** if it doesn't exist:
   ```bash
   mkdir -p docs/plans
   ```

2. **Generate filename**: `YYYY-MM-DD-<type>-<sanitized-name>-plan.md` where type is one of: feature, bugfix, refactor, migration, optimization.

3. **Write plan file** with this structure:

   ```markdown
   # Plan: [Feature/Task Name]

   **Date:** YYYY-MM-DD
   **Type:** [feature|bugfix|refactor|migration|optimization]
   **Status:** Draft

   ## Knowledge Context

   ### Relevant Learnings
   - [Learning title](../knowledge/solutions/category/filename.md): [key insight]

   ### Known Gotchas
   - [Gotcha description and how the plan avoids it]

   ### Critical Patterns Applied
   - [Pattern and where it's applied in this plan]

   ## Overview

   [Brief description of what this plan accomplishes]

   ## Implementation Steps

   ### Step 1: [Description]
   - **Files:** `path/to/file.ext`
   - **Details:** What to do
   - **Knowledge note:** [If applicable, reference to relevant learning]

   ### Step 2: [Description]
   ...

   ## Risks and Mitigations

   | Risk | Likelihood | Mitigation |
   |------|-----------|------------|
   | [risk] | [high/medium/low] | [mitigation strategy] |

   ## Testing Strategy

   - [ ] [Test case 1]
   - [ ] [Test case 2]

   ## Open Questions

   - [Any unresolved questions]
   ```

4. **Confirm output.** Tell the user where the plan was saved and suggest:
   - `/work <plan-file>` to start executing the plan
   - Review and refine the plan first

## Arguments

- `$ARGUMENTS` (required): feature description, path to a brainstorm/spec file, or inline specification.

</instructions>
