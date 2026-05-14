---
name: vue-tailwind-vuetify-typescript
description: Vue UI guidance for using Tailwind utility classes, Vuetify components, and TypeScript-first component contracts.
---

# Vue Tailwind Vuetify TypeScript

Use this skill for Vue UI work when the project uses Tailwind CSS, Vuetify, and TypeScript together.

## Tailwind

- Prefer existing utility patterns already used in the repo.
- Use utility classes for spacing, layout, alignment, and simple visual adjustments.
- Avoid building large custom class strings when a reusable pattern exists.

## Vuetify

- Use Vuetify components, props, slots, and theming where the project already relies on them.
- Prefer built-in Vuetify layout and form primitives over custom markup when it fits the existing design system.
- Keep Vuetify usage consistent across the feature area.

## TypeScript

- Type props, emits, refs, computed values, composables, and store state explicitly.
- Avoid `any`, broad casts, and hidden `unknown` escapes.
- Keep component contracts narrow and well-typed.

## Combined Rule

- Follow the existing local balance between Tailwind, Vuetify, and custom CSS.
- Do not introduce a new UI strategy unless the approved plan explicitly requires it.
- Keep styling choices local and consistent with the surrounding feature.
