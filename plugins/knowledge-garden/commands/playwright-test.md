---
name: knowledge-garden::playwright-test
description: Write, run, and analyze Playwright E2E tests for your project
argument-hint: "[feature, route, or file path to test]"
allowed-tools:
  - Read
  - Write
  - Edit
  - Bash
  - Glob
  - Grep
  - AskUserQuestion
---

# Playwright Testing

Write, run, and analyze Playwright E2E tests following project conventions with accessible locators and failure diagnosis.

<instructions>

## Execution

Execute the full playwright-test skill workflow:

1. **Detect test context** from `$ARGUMENTS` or `git diff --name-only`. Classify as API, E2E, or full-stack scope. If unclear, ask the user what to test.

2. **Check Playwright setup** -- verify `@playwright/test` is installed and `playwright.config.*` exists. Offer to install if missing.

3. **Discover existing tests** -- scan `*.spec.ts` files and read up to 5 to learn project conventions (imports, page objects, auth setup, naming). Check knowledge base for past test-related solutions.

4. **Write or update tests** -- present a test plan to the user before writing. Follow project conventions and use accessible locators (`getByRole`, `getByLabel`, `getByText`). Wait for approval before creating test files.

5. **Run tests** -- verify the dev server is running, then execute with `npx playwright test [file] --reporter=list`. If the server is not running, ask the user to start it.

6. **Analyze results and fix** -- if tests fail, diagnose each failure (locator issue, timing, data, app bug, environment), present findings, and offer to fix and re-run. Limit to 3 retry cycles.

7. **Knowledge capture (optional)** -- if non-trivial issues were encountered (flaky patterns, environment gotchas, framework quirks), recommend `/knowledge-garden::compound` to document the learning.

## Arguments

- `$ARGUMENTS` (optional): feature name, route path, or file path to test. If omitted, detects from recent git changes.

  Examples:
  - `merchant creation form`
  - `/merchants/create`
  - `src/app/merchants/page.tsx`
  - `login flow with 2FA`

</instructions>
