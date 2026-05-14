---
name: vue-options-api-best-practices
description: Options API guidance for Vue 3 projects that intentionally use data, methods, computed, watch, and this-based component style.
---

# Vue Options API Best Practices

Use this skill only when the project explicitly uses Options API.

## Rules

- Keep `data`, `computed`, `methods`, `watch`, and lifecycle hooks conventional and explicit.
- Preserve `this` usage patterns and avoid mixing styles unless the project already does.
- Type props, emits, and event handlers carefully in TypeScript projects.
- Avoid arrow functions where they break `this` binding in methods or hooks.

## Debugging

- Check that lifecycle hooks and methods have the expected component instance context.
- Confirm watcher timing and prop default behavior.
- Keep component contracts explicit and documented.
