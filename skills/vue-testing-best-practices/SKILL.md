---
name: vue-testing-best-practices
description: Vue testing guidance for Vitest, Vue Test Utils, Playwright, and behavior-focused test design.
---

# Vue Testing Best Practices

Use this skill when adding or updating Vue tests.

## Rules

- Prefer behavior tests over implementation-detail tests.
- Use the smallest useful seam: composable, store, component, or route depending on the change.
- Prefer real DOM interactions when practical.
- Mock only unavoidable external boundaries.
- Keep snapshots out of the default approach unless the project already relies on them.

## Vue-Specific Checks

- Verify async rendering and update timing.
- Handle Pinia setup and router dependencies explicitly.
- Use Playwright for browser-observable behavior when component tests are not enough.
