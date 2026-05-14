---
name: vue-debug-guides
description: Vue debugging guidance for runtime errors, reactivity issues, watcher problems, props/emits mistakes, and SSR or hydration bugs.
---

# Vue Debug Guides

Use this skill when diagnosing Vue bugs or confusing runtime behavior.

## Common Debug Areas

- Reactivity: refs, reactive objects, computed values, watchers, and stale dependencies.
- Components: prop contracts, emits, slots, template refs, component naming, and lifecycle timing.
- Forms: v-model, validation, input state, and reset behavior.
- Routing: params, navigation guards, cleanup, and route lifecycle issues.
- SSR/Hydration: client/server mismatch and browser-only APIs in universal code.

## Debug Rules

- Confirm the real failing path before changing code.
- Prefer small, local fixes over broad refactors.
- Check console errors, warning messages, and the exact reactive path that changed.
- Verify whether the bug is caused by stale values, missing `.value`, destructuring, or wrong watcher timing.

## Final Check

- Preserve the existing data flow.
- Fix the root cause, not just the symptom.
- Add or update focused verification when the bug affects user-visible behavior.
