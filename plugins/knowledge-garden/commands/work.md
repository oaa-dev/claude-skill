---
name: knowledge-garden::work
description: Execute work with knowledge guardrails and incremental commits
argument-hint: "[plan file, task description, or specification]"
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

# Knowledge-Guarded Work Execution

Execute a plan or task systematically with knowledge guardrails that surface relevant learnings before touching each module.

<instructions>

## Phase 1: Knowledge Auto-Read

Before starting work, surface relevant institutional knowledge using the pre-computed index.

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
   ```
   Read `docs/knowledge/patterns/critical-patterns.md`. Skip to Step 5.
   </details>

2. **Check if empty.** If the index header contains `<!-- Solutions: 0 -->`, state: "Knowledge base is empty. Skipping knowledge context." Proceed directly to Phase 2.

3. **Read the work input.** If `$ARGUMENTS` points to a file (plan, spec, or todo), read it fully. Otherwise treat `$ARGUMENTS` as the task description.

4. **Scan index in-context.** Extract keywords from the work input and scan the Solutions table and Critical Patterns section for matches. No tool calls needed -- the index is already in context.

5. **Deep-read strong matches.** For rows where Module, Type, Tags, or Critical Patterns strongly match (typically 0-3 files), read the full solution file for detailed context.

6. **Present knowledge briefing:**

   ```
   ## Pre-Work Knowledge Briefing

   **Relevant learnings:** X documents
   - [Title]: [key insight]

   **Watch out for:**
   - [Known issues in modules being touched]

   **Critical patterns to follow:**
   - [Applicable patterns]
   ```

## Phase 2: Task Breakdown & Dependency Analysis

1. **Parse tasks.** Break the work into discrete, ordered tasks. If the input is a plan file, extract steps directly from it.

2. **Annotate file scope.** For each task, identify which files it will create or modify:
   - Extract file references from the plan (paths, module names, component names)
   - If the plan doesn't specify files, infer from task descriptions using Glob/Grep to locate relevant code

3. **Build dependency graph.** Analyze tasks for dependencies:
   - **Explicit ordering**: task descriptions that say "after X", "requires Y", "once Z is done"
   - **Data dependencies**: task B uses a type/function/export that task A creates
   - **File overlap**: if two tasks modify the same file, they MUST be sequential (never parallel)

4. **Form parallel waves.** Group tasks into waves of independent work:
   - **Wave 1**: tasks with no dependencies (can all run in parallel)
   - **Wave 2**: tasks that depend only on Wave 1 tasks
   - **Wave N**: tasks that depend only on tasks in earlier waves
   - Tasks within a wave have zero dependencies on each other and zero file overlap

5. **Create task list.** Use TodoWrite to track progress:
   - Each task should be small enough for one commit
   - Include wave assignment and file scope per task
   - Flag tasks that touch modules with documented issues

6. **Present execution plan.** Show the user the wave structure:

   ```
   ## Execution Plan

   **Wave 1** (parallel - N tasks):
   - Task A: [description] → files: [list]
   - Task B: [description] → files: [list]

   **Wave 2** (parallel - N tasks, after Wave 1):
   - Task C: [description] → files: [list]

   **Wave 3** (serial - depends on Wave 2):
   - Task D: [description] → files: [list]
   ```

   Ask the user:
   - **Parallel mode** (recommended when 2+ tasks in any wave): dispatch independent tasks as parallel agents
   - **Serial mode**: execute all tasks one at a time (classic behavior)

   If all tasks are in a single wave chain (every task depends on the previous), skip the question and use serial mode automatically.

## Phase 3: Execute Tasks

### Serial Mode

Use when the user chooses serial, or when all tasks are dependent.

For each task in order:

1. **Pre-task knowledge check.** Before starting a task, check if the specific module/component being touched has documented issues:
   ```
   Grep: pattern="module:.*[ModuleName]" path=docs/knowledge/solutions/ output_mode=files_with_matches -i=true
   ```
   If matches found, quickly read frontmatter to surface gotchas relevant to this specific task.

2. **Execute the task.** Implement the change following established patterns and knowledge guardrails.

3. **Verify.** Run relevant tests or checks after each task:
   ```bash
   # Run tests if available
   # Lint if configured
   # Type-check if applicable
   ```

4. **Commit incrementally.** After each completed task, suggest a commit with a descriptive message. Ask the user if they want to commit now or continue.

5. **Update progress.** Mark the task as complete in the todo list.

### Parallel Mode

Use when the user chooses parallel and at least one wave has 2+ independent tasks.

For each wave:

1. **Prepare knowledge context.** Before dispatching, gather all relevant knowledge for the wave's tasks in a single pass:
   - Scan the knowledge index for modules/components touched by any task in this wave
   - Deep-read matching solutions (typically 0-3 files)
   - Compile a knowledge briefing to pass to each worker

2. **Dispatch task-worker agents.** Launch all tasks in the wave simultaneously using `Task(run_in_background: true)` -- all in a single message for true parallelism:

   ```
   For each task in the current wave, dispatch a task-worker agent:

   Task(
     subagent_type: "knowledge-garden:learnings-researcher",  -- or "general-purpose"
     description: "Execute task: [brief]",
     prompt: """
       ## Task
       [task description]

       ## File Scope
       [files this task should create/modify]

       ## Knowledge Context
       [relevant learnings, patterns, gotchas compiled in step 1]

       ## Plan Context
       [enough plan context to understand the broader goal]
     """,
     run_in_background: true
   )
   ```

   Use `subagent_type: "general-purpose"` for the task-worker agents. The task-worker agent prompt (from `agents/task-worker.md`) should be included in the dispatch prompt so the agent knows its constraints (no commits, no user questions, stay in file scope).

3. **Collect results.** Wait for all background agents to complete. Review each agent's structured result (status, files changed, tests, warnings).

4. **Run full test suite.** After all workers in the wave complete, run the project's full test suite to catch cross-task conflicts:
   ```bash
   # Run full test suite (project-specific command)
   ```
   If tests fail, diagnose whether the conflict is between parallel tasks or an individual task issue. Fix before proceeding.

5. **Present wave results.** Show the user a summary:

   ```
   ## Wave N Results

   **Task A**: completed -- changed file1.ext, file2.ext
   **Task B**: completed -- changed file3.ext
   **Task C**: partial -- [issue description]

   **Tests**: all passing | N failures
   **Warnings**: [any cross-task issues]
   ```

   Ask the user:
   - Commit all wave changes together (recommended)
   - Commit per task
   - Fix issues first

6. **Proceed to next wave.** After the user approves the commit, move to the next wave. Tasks in the next wave can now reference outputs from this wave.

## Phase 4: Post-Work Knowledge Capture

After all tasks are complete:

1. **Review what was done.** Summarize the work completed.

2. **Prompt for knowledge capture.** Ask the user:

   ```
   Work complete. Did you encounter any non-trivial problems worth documenting?

   1. Yes - capture learnings now (/compound)
   2. No - nothing worth documenting
   3. Later - remind me next session
   ```

   If the user selects "Yes", suggest running `/compound` to invoke the knowledge-docs skill.

3. **Report completion.** Summarize:
   - Tasks completed
   - Files changed
   - Any knowledge-backed decisions made during execution

## Arguments

- `$ARGUMENTS` (required): path to a plan file, task description, or inline specification.

</instructions>
