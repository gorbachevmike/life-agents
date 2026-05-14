---
name: vue-router-best-practices
description: Vue Router guidance for navigation guards, params, cleanup, and route-component lifecycle interactions.
---

# Vue Router Best Practices

Use this skill for Vue Router routes, guards, and navigation behavior.

## Rules

- Keep route contracts explicit: params, query, names, and guards.
- Use the simplest guard or route hook that matches the behavior.
- Clean up side effects when route components unmount or change.
- Avoid infinite redirect loops and stale route data.

## Debugging

- Check whether same-route navigation reuses a component instance.
- Verify param/query updates on route changes.
- Ensure route guards await async work before deciding navigation.
