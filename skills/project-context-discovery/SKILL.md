---
name: project-context-discovery
description: Find the smallest relevant project context from instructions, docs, package metadata, TypeScript config, and repository structure before planning or navigation.
---

# When To Use

Use this skill when an agent needs repository context before planning, code navigation, bug investigation, or test selection.

Use it to discover project-specific facts. Do not use it for broad repository audits.

# Procedure

1. Read root project instructions such as `AGENTS.md` when present.
2. Inspect the top-level repository structure just enough to identify app, package, frontend, backend, shared, and test boundaries.
3. Inspect root `package.json` when present to identify package manager, workspaces, scripts, and framework signals.
4. In monorepos, inspect only the package-level `package.json` files that are relevant to the task area.
5. Inspect `tsconfig.json` or `tsconfig.*.json` only when aliases, source roots, or project references are needed.
6. If `docs/` exists, list top-level documentation areas and search by task keywords, filenames, headings, and directory names.
7. Read only the smallest useful subset of docs. If more than five docs look relevant, choose the top three to five.
8. Prefer project-specific docs and instructions over generic framework assumptions.
9. If docs and code appear to conflict, report the conflict instead of guessing.

# Keyword Guidance

Derive keywords from:

- user-visible labels;
- route names;
- component names;
- hook/composable names;
- store or service names;
- API methods and types;
- domain terms;
- error messages;
- English and Russian synonyms when the task is in Russian.

# Output Expectations

Return compact context evidence:

```markdown
## Project Context
- Instructions: `<path>` / not found
- Package metadata: `<path>` / not needed
- TypeScript config: `<path>` / not needed
- Docs: `<path>` / No relevant docs found
- Project boundaries: <short summary>
- Constraints: <important project-specific rules>
- Unknowns: <missing or conflicting context, or `None known`>
```

# Quality Criteria

A good result:

- reads only relevant context;
- cites paths used as evidence;
- avoids fixed docs filenames;
- avoids broad scans;
- distinguishes confirmed facts from assumptions;
- reports unknowns explicitly.
