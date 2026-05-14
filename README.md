# Life Agents

Переиспользуемые OpenCode-агенты и skills для frontend-разработки.

Версия: `0.1`

## Что Внутри

Агенты находятся в `agents/`:

- `frontend-dev-assistant` - основной агент для frontend-задач. Классифицирует задачу, собирает контекст, составляет план до правок, делегирует специализированным агентам, запускает проверки и возвращает отчет.
- `code-navigator` - read-only subagent для навигации по коду. Находит релевантные файлы, точки входа, execution path, связи и похожие реализации.
- `bugfix-investigator` - read-only subagent для расследования багов. Воспроизводит или анализирует симптом, строит гипотезы и предлагает минимальный фикс.
- `vue-frontend-engineer` - subagent-исполнитель для Vue-задач. Реализует утвержденные изменения в SFC, Composition API, stores, routes и reactivity, сохраняя локальные паттерны.
- `electron-engineer` - subagent-исполнитель для Electron-задач. Реализует утвержденные изменения в main/preload/renderer, IPC и runtime boundary, сохраняя безопасность контрактов.
- `test-writer` - subagent для тестов. Находит тестовый стек, выбирает минимально полезную testing seam, пишет behavior tests и запускает только релевантные тесты.
- `browser-verifier` - read-only subagent для browser verification через Chrome DevTools MCP. Проверяет живой UI, a11y snapshot, console, network, screenshots, Lighthouse и performance trace.
- `reviewer` - read-only subagent для поиска рисков. Проверяет API breaks, render/state issues, type risks, циклические зависимости, overengineering и отсутствие проверок.
- `commit-assistant` - основной агент для коммитов по git diff. Группирует изменения, ищет мусор, предлагает commit message и коммитит только после явного подтверждения.

Skills находятся в `skills/`:

- `project-context-discovery` - находит проектные инструкции, docs, package metadata, TypeScript config и границы проекта.
- `testing-seam-selection` - выбирает минимально полезную testing seam и защищает от чрезмерных моков.
- `frontend-risk-review` - переиспользуемый frontend risk checklist для review и self-review fallback.

## Установка

Скопировать агентов в пользовательскую конфигурацию OpenCode:

```bash
cp agents/*.md ~/.config/opencode/agents/
```

Скопировать skills в пользовательскую конфигурацию OpenCode:

```bash
cp -r skills/* ~/.config/opencode/skills/
```

Если нужны project-local agents, скопируйте их прямо в проект:

```bash
mkdir -p .opencode/agents .opencode/skills
cp agents/*.md .opencode/agents/
cp -r skills/* .opencode/skills/
```

## Chrome DevTools MCP

`browser-verifier` ожидает, что Chrome DevTools MCP уже подключен в OpenCode. Это отдельный MCP-сервер от Chrome DevTools, который дает агенту доступ к живому Chrome для browser automation, accessibility snapshot, console/network inspection, screenshots, Lighthouse и performance trace.

Минимальные требования к окружению:

- Chrome DevTools MCP установлен и доступен в OpenCode.
- Tool names доступны как `list_pages`, `navigate_page`, `take_snapshot`, `click`, `fill`, `list_console_messages`, `list_network_requests`, `performance_start_trace`, `lighthouse_audit` и другие Chrome DevTools MCP tools.
- Если ваша OpenCode-конфигурация добавляет prefix к MCP tool names, перенесите allowlist из `agents/browser-verifier.md` на реальные имена инструментов.
- Для UI-проверок проект должен иметь безопасный target URL, например локальный dev server `http://localhost:5173`.
- Для сценариев с авторизацией нужны тестовые учетные данные или заранее подготовленная активная browser session.

`browser-verifier` не запускает install/build/server команды сам по умолчанию. Основной агент должен получить URL из `AGENTS.md`, package scripts, пользователя или уже открытой вкладки.

## Как Сформировать AGENTS.md

`AGENTS.md` должен давать агентам проверяемые правила проекта: где находится код, какие команды запускать, какие границы нельзя нарушать и какие проверки считать обязательными.

Для монорепозитория лучше использовать два уровня инструкций:

- Корневой `AGENTS.md` описывает правила всего репозитория, workspace layout, пакетный менеджер, общие команды и запреты.
- Package-level `AGENTS.md` внутри приложения или пакета описывает локальный стек, команды, тесты, архитектурные границы и особенности конкретной части монорепозитория.

Корневой `AGENTS.md` должен быть коротким и не дублировать детали всех пакетов. Его цель - помочь агенту быстро понять границы монорепозитория и выбрать правильный package-level контекст.

Рекомендуемый корневой шаблон:

```markdown
# AGENTS.md

## Repository

- Type: monorepo
- Package manager: pnpm / npm / yarn / bun
- Workspace config: `pnpm-workspace.yaml` / `package.json#workspaces` / другое
- Node version: `<version or source, например .nvmrc>`

## Package Map

- `apps/web`: frontend app, Vue / React / Next / Vite
- `apps/desktop`: Electron app, main/preload/renderer
- `packages/ui`: shared UI components
- `packages/shared`: shared types and utilities
- `packages/api`: API client or backend package

## Global Rules

- Do not run install commands unless explicitly approved.
- Do not change lock files, workspace config, build config, generated files, or dependency versions unless explicitly approved.
- Do not cross package boundaries by importing private files from another package.
- Prefer existing package scripts over ad-hoc commands.
- Preserve public exports, shared types, API contracts, IPC contracts, route contracts, and package boundaries.

## Context Rules

- Start with this file, then read the nearest package-level `AGENTS.md` for the touched package.
- If multiple packages are affected, identify each package and its local instructions before planning.
- If package-level instructions conflict with this file, stop and report the conflict.

## Verification

- Root typecheck: `<command or not available>`
- Root lint: `<command or not available>`
- Root tests: `<command or not available>`
- Prefer package-level focused commands for normal feature and bugfix work.

## Required Delegation

- Frontend implementation starts through `frontend-dev-assistant`.
- Vue implementation should be delegated to `vue-frontend-engineer`.
- Electron implementation should be delegated to `electron-engineer`.
- Behavior-changing work should delegate tests to `test-writer`.
- Browser-observable UI work should delegate live verification to `browser-verifier` when a safe target URL exists.
- Final risk review should be delegated to `reviewer`.
```

Package-level `AGENTS.md` кладите в директорию конкретного приложения или пакета, например `apps/web/AGENTS.md`, `apps/desktop/AGENTS.md`, `packages/ui/AGENTS.md`.

Рекомендуемый package-level шаблон:

```markdown
# AGENTS.md

## Package

- Name: `apps/web`
- Type: frontend app / Electron app / shared library / API package
- Framework: Vue / React / Electron / Vite / Next / другое
- Source root: `src/`
- Public entrypoints: `src/index.ts`, `src/main.ts`, `src/preload.ts`, routes, exports

## Local Architecture

- Components: `<paths>`
- Routes/pages: `<paths>`
- Stores/state: `<paths>`
- Services/API clients: `<paths>`
- Shared types/contracts: `<paths>`
- Tests: `<paths>`

## Local Rules

- Follow existing component, composable, store, service, route, and test patterns.
- Do not import from another package's private `src/` files. Use public package exports.
- Do not change public exports, props, events, routes, schemas, IPC channels, or shared types without calling it out in the plan.
- Keep changes inside this package unless the approved plan names another package.

## Commands

- Typecheck: `<package-level command>`
- Lint: `<package-level command>`
- Unit tests: `<package-level command>`
- Focused test: `<example focused command>`
- Build: `<package-level command or not available>`
- Dev server: `<command and default local URL, for example http://localhost:5173>`

## Testing Guidance

- Test stack: Vitest / Jest / Playwright / Cypress / другое
- Test setup: `<setup file or not available>`
- Prefer the smallest behavior test near the changed unit.
- Do not add testing dependencies or new infrastructure without explicit approval.

## Browser Verification

- Local URL: `<default dev URL or not available>`
- Safe demo/test account: `<how to get it or not needed>`
- Seed data: `<command or not needed>`
- Flows safe for browser automation: `<routes/forms/actions>`
- Flows requiring explicit approval: payments, destructive actions, uploads, production mutations, external emails, account changes.

## Technology Notes

- Vue: describe Composition API, Pinia/Vuex, Router, UI library, form validation, and styling conventions.
- Electron: describe main/preload/renderer layout, IPC naming, exposed preload APIs, security constraints, and manual runtime checks.
- React: describe routing, state, data fetching, styling, and compiler/memoization conventions.
```

При заполнении шаблонов используйте конкретные команды из `package.json`, реальные пути и реальные границы пакетов. Не добавляйте желаемые правила, которых проект фактически не соблюдает, иначе агенты будут строить планы на ложном контексте.

Для больших монорепозиториев полезно добавить отдельный `AGENTS.md` только в те пакеты, где агенты реально должны работать. Если файла в пакете нет, агент будет опираться на корневые инструкции, `package.json`, docs и найденные локальные паттерны.

## Рекомендуемый Workflow

Для обычной frontend-разработки используйте `frontend-dev-assistant`:

```text
@frontend-dev-assistant добавить поиск в список инстансов
```

Ожидаемый процесс:

1. Классифицирует задачу как `bugfix`, `feature`, `test`, `refactor`, `review` или `explain`.
2. Собирает контекст проекта и релевантные docs.
3. Делегирует поиск кода агенту `code-navigator`.
4. Для нетривиальных багов делегирует расследование агенту `bugfix-investigator`.
5. Определяет technology routing: `vue-frontend-engineer`, `electron-engineer`, оба или не нужно.
6. Составляет конкретный план и ждет явного подтверждения.
7. После подтверждения делегирует Vue/Electron реализацию соответствующим subagents, если задача касается этих технологий.
8. Вызывает `test-writer`, если изменилось поведение или нужны тесты.
9. Вызывает `browser-verifier`, если изменение видно в браузере и есть безопасный URL/сценарий для проверки.
10. Запускает проверки из проектных инструкций.
11. Вызывает `reviewer` перед финальным отчетом.
12. Возвращает, что изменено, почему и как проверено.

Когда изменения готовы к коммиту, используйте `commit-assistant`:

```text
@commit-assistant подготовь коммит
```

Он использует только git context: `git status`, `git diff`, staged diff и свежий `git log`. Он не делает `git add` и `git commit` без явного подтверждения.

## Модель Безопасности

- Агенты, которые могут менять код, сначала требуют план.
- `code-navigator`, `bugfix-investigator` и `reviewer` работают read-only.
- `browser-verifier` работает read-only для кода и использует Chrome DevTools MCP только для безопасной проверки живого браузера.
- `vue-frontend-engineer` и `electron-engineer` меняют код только после утвержденного плана от primary agent и не пишут тесты без отдельного fallback-разрешения.
- `test-writer` может менять тесты, но не добавляет зависимости и тестовую инфраструктуру без подтверждения.
- `commit-assistant` не читает исходники напрямую и не коммитит без явного подтверждения.
- Skills подключены через allowlist для конкретных агентов, а не включены глобально.

## Заметки По Версии 0.1

Эта версия ориентирована на frontend TypeScript/Vue/React/Electron-проекты, но агенты должны адаптироваться к локальным правилам проекта через discovery репозитория.

Текущие ограничения:

- `test-writer` не добавляет новый тестовый стек, если его нет в проекте.
- `commit-assistant` намеренно использует только git diff/status/log context.
- `reviewer` сообщает о рисках, но не исправляет их.
- `browser-verifier` требует настроенный Chrome DevTools MCP и безопасный target URL или активную страницу.
- Агенты ожидают, что supporting subagents установлены под теми же именами.
- Если обязательный subagent недоступен, `frontend-dev-assistant` должен остановиться и запросить явное разрешение на fallback, а не молча делать работу сам.

## Первый Тестовый Запуск

В реальном проекте попробуйте:

```text
@frontend-dev-assistant объясни, как устроен основной frontend route flow
```

Затем попробуйте маленькое изменение поведения и проверьте, что агент сначала составит план, а не начнет сразу редактировать код.
