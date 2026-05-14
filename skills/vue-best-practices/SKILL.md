---
name: vue-best-practices
description: Vue 3 implementation guidance for Composition API, script setup, component boundaries, reactivity, and explicit data flow.
---

# Vue Best Practices

Use this skill for Vue tasks that touch components, composables, routing, state, or template structure.

## Defaults

- Prefer Vue 3 Composition API and `<script setup lang="ts">` unless the project explicitly uses Options API.
- Keep state minimal and derive everything possible with `computed`.
- Make data flow explicit: props down, events up, `v-model` only for real two-way contracts.
- Keep components small and focused; split when one component owns both orchestration and substantial UI.

## SFC Guidance

- Keep templates declarative and move branching/derivation into script.
- Keep SFC responsibilities narrow.
- Preserve existing component boundaries, naming, and folder layout.

## Reactivity Guidance

- Avoid prop mutation, watch loops, and side effects inside computed values.
- Use watchers for side effects and `computed` for derived data.
- Keep async state transitions deterministic and guard stale results when needed.

## Composition Guidance

- Extract reusable or side-effect-heavy logic into composables.
- Keep composable APIs small, typed, and predictable.
- Prefer explicit props/emits over implicit shared state.

## Performance Guidance

- Avoid unnecessary re-renders from unstable object/function creation.
- Keep expensive logic out of templates.
- Optimize only after correct behavior is in place.
