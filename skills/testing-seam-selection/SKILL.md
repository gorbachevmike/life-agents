---
name: testing-seam-selection
description: Choose the smallest useful testing seam by discovering the test stack, following local patterns, preferring behavior tests, and treating coverage as diagnostic only.
---

# When To Use

Use this skill when an agent needs to plan or write tests for changed behavior, bug regression coverage, explicit test tasks, or risky refactors.

# Core Rule

Prefer real behavior tests over excessive mocks.

Do not introduce broad mocking unless external IO, timers, network, browser APIs, Electron APIs, or process boundaries require it.

# Procedure

1. Identify the test stack from repository evidence: `package.json`, test scripts, config files, dependencies, setup files, and existing tests.
2. Search for the closest existing test patterns in this order: same file or feature, same unit type, same package, then repository-wide examples.
3. If no tests exist but a test stack is configured, choose the smallest first example that follows project language, naming, and layout.
4. If no test stack is configured, do not add dependencies or package scripts. Return a setup recommendation and ask for approval.
5. Choose the smallest seam that proves changed behavior without overfitting to implementation details.
6. Prefer seams in this order when practical: pure function, hook/composable, store/state transition, service with external boundaries mocked, component behavior, route/page behavior, e2e/browser.
7. Mock only the unavoidable boundary. Do not mock the module under test.
8. Choose the narrowest relevant test command or test file to run.
9. If coverage tooling already exists and can run narrowly, report file-level coverage for the tested file or nearest changed unit.
10. Use coverage as a diagnostic signal, not as the primary goal.

# Coverage Guidance

- Prioritize changed behavior and meaningful branches over arbitrary percentages.
- Treat 80%+ file coverage as a useful signal, not a hard requirement.
- Do not add low-value assertions only to increase coverage.
- Do not add coverage tooling or modify scripts without explicit approval.
- If coverage cannot be collected narrowly, report `not available` and explain why.

# Output Expectations

Return compact test guidance:

```markdown
## Testing Seam
- Test stack: Vitest / Jest / Playwright / Cypress / unknown
- Existing pattern: `<path>` / none found
- Chosen seam: <function/composable/store/service/component/route/e2e>
- Reason: <why this is the smallest useful seam>
- Mocks: <none / external boundary and why>
- Relevant command: `<command>` / not available
- Coverage: `<file>` <percent or `not available`>
- Unknowns: <remaining uncertainty or `None known`>
```

# Quality Criteria

A good result:

- is based on repository evidence;
- follows local test patterns;
- tests behavior, not private implementation;
- avoids broad mocking;
- avoids dependency changes unless explicitly approved;
- recommends focused verification;
- treats coverage as diagnostic, not as the reason to write weak tests.
