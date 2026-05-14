---
name: frontend-risk-review
description: Review frontend changes for public API breaks, render/reactivity issues, state mutation, type risks, cyclic dependencies, overengineering, scope creep, and missing verification.
---

# When To Use

Use this skill for read-only review, self-review fallback, or final risk checks before reporting or committing frontend changes.

# Review Focus

Check whether the change:

- breaks public API, exports, props, events, routes, service contracts, IPC contracts, schemas, or shared types;
- changes behavior outside the approved scope;
- introduces unnecessary re-renders or unstable object/function identities in render paths;
- mutates state in render, computed values, watchers, effects, selectors, or derived state;
- creates recursive Vue or React updates, watch/effect loops, stale closures, or async race risks;
- weakens type safety through `any`, unsafe casts, broad optional values, ignored errors, or assertions hiding invalid states;
- adds hidden cyclic dependencies or suspicious cross-layer imports;
- adds overengineering, premature abstractions, or unnecessary indirection;
- is less local than the bug or feature requires;
- lacks a test or manual verification for changed behavior.

# Procedure

1. Start with the diff or changed files and the approved task scope.
2. Identify changed files and their roles.
3. Check public API and contract surfaces first.
4. Check render/reactivity/state mutation risks in touched paths.
5. Check type safety and suspicious casts or weakened contracts.
6. Check dependency direction and possible cycles.
7. Check whether the implementation is local and proportional.
8. Check whether tests or manual verification cover the changed behavior.
9. Return findings first, sorted by severity.
10. If no issues are found, say `No findings` explicitly and note residual risks.

# Severity Rules

Use `high` for likely runtime breaks, API or contract breaks, data loss, infinite updates, broken build/type safety in touched paths, or security/process-boundary violations.

Use `medium` for plausible regressions, missing important tests, hidden cycle risks, unsafe type weakening, unclear non-local fixes, async races, or happy-path-only behavior.

Use `low` for minor overengineering, unclear naming, low-blast-radius manual verification gaps, or local complexity that is not yet a correctness bug.

# Output Expectations

Return findings first:

```markdown
## Findings
- Severity: high / medium / low
- File: `<path>:<line>`
- Issue: <what can break>
- Evidence: <why this is a real risk>
- Recommendation: <minimal action>

## Checks Reviewed
- Public API/exports: pass / risk found / not applicable
- Render/reactivity: pass / risk found / not applicable
- State mutation: pass / risk found / not applicable
- Types: pass / risk found / not applicable
- Cyclic dependencies: pass / risk found / not applicable
- Scope/overengineering: pass / risk found / not applicable
- Tests/verification: pass / risk found / not applicable

## Residual Risks
- <risk or `None known`>
```

# Quality Criteria

A good result:

- focuses on real risks over style preferences;
- cites concrete files and lines when possible;
- sorts findings by severity;
- explicitly says `No findings` when appropriate;
- identifies missing tests or manual checks when relevant;
- avoids broad refactor advice unless tied to concrete risk;
- remains read-only.
