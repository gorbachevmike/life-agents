---
name: vue-pinia-best-practices
description: Pinia guidance for Vue stores, state ownership, reactivity, SSR, and store contract safety.
---

# Vue Pinia Best Practices

Use this skill for Pinia stores and Vue state management.

## Rules

- Keep a single source of truth in the store and derive view state with getters/computed values.
- Avoid destructuring that breaks reactivity; keep store usage reactive.
- Preserve action names and state contracts unless the plan explicitly changes them.
- Keep stores small and feature-focused.
- Be careful with SSR and store initialization.

## Debugging

- Check active Pinia setup and store injection.
- Verify that destructured values still update.
- Ensure store methods keep the correct context.
