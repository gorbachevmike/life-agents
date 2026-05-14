---
name: create-adaptable-composable
description: Build Vue composables that accept MaybeRef or MaybeRefOrGetter inputs and normalize them with toRef/toValue inside reactive effects.
---

# Create Adaptable Composable

Use this skill when creating reusable Vue composables that should accept plain values, refs, or getters.

## Rules

- Use `MaybeRefOrGetter` for read-mostly inputs.
- Use `MaybeRef` for writable or two-way inputs.
- Normalize inputs with `toRef()` or `toValue()` inside watchers or watchEffects.
- Keep composable APIs small, predictable, and typed.

## Design Pattern

1. Confirm the composable purpose and inputs.
2. Decide which inputs need reactivity.
3. Normalize with Vue reactivity helpers.
4. Keep side effects explicit and local.
