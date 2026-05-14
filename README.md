# Life Agents

Переиспользуемые OpenCode-агенты и skills для разработки в продуктовых и enterprise-проектах.

Версия: `0.1`

## Что Внутри

Агенты находятся в `agents/`:

- `frontend-dev-assistant` - основной агент для frontend-задач. Классифицирует задачу, собирает контекст, составляет план до правок, делегирует специализированным агентам, запускает проверки и возвращает отчет.
- `java-dev-assistant` - основной агент для Java/backend-задач. Работает с русскими запросами, Maven/Gradle, Spring/JVM, REST, JMS IBM MQ/ArtemisMQ, workflow, внутренними библиотеками и Bitbucket source lookup через subagent.
- `code-navigator` - read-only subagent для навигации по коду. Находит релевантные файлы, точки входа, execution path, связи и похожие реализации.
- `bitbucket-source-navigator` - read-only subagent для точечного поиска исходников внутренних библиотек и шаблонов через Bitbucket MCP по dependency/package/class/contract/template evidence.
- `bugfix-investigator` - read-only subagent для расследования багов. Воспроизводит или анализирует симптом, строит гипотезы и предлагает минимальный фикс.
- `test-writer` - subagent для тестов. Находит тестовый стек, выбирает минимально полезную testing seam, пишет behavior tests и запускает только релевантные тесты.
- `reviewer` - read-only subagent для поиска рисков. Проверяет API breaks, render/state issues, type risks, циклические зависимости, overengineering и отсутствие проверок.
- `git-assistant` - основной агент для git-операций. Группирует изменения, создает коммиты в формате `TEAMCVSI-${number} Краткое сообщение на русском` и открывает Bitbucket MR через MCP только после явного подтверждения.

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

## Генерация AGENTS.md

В каждом рабочем проекте рекомендуется создать project-local `AGENTS.md`. Этот файл должен описывать правила проекта, команды проверки, границы модулей и внутренние enterprise-конвенции, чтобы агенты не угадывали контекст.

Запустите OpenCode из корня целевого проекта и используйте такой запрос:

```text
Сгенерируй AGENTS.md для этого проекта.

Сначала изучи только локальный контекст: README, docs, build/package metadata, структуру модулей, доступные test/build/lint команды, frontend/backend/shared/generated границы, workflow/JMS/contract конфигурацию и существующие правила разработки.

Не меняй код. Не запускай install, build, tests, migrations или destructive commands без отдельного подтверждения.

Подготовь AGENTS.md с разделами:
- Project Overview
- Repository Structure
- Module Boundaries
- Build And Verification Commands
- Coding Conventions
- Testing Conventions
- Generated Files And Contracts
- Internal Libraries And Starters
- Workflow/JMS Notes
- Bitbucket Repository Map, если есть точные соответствия dependency/package -> project/repo
- Safety Rules

Не добавляй секреты, токены, приватные URL с credentials или локальные machine-specific paths.
Перед записью файла покажи план и дождись подтверждения.
```

После генерации проверьте, что `AGENTS.md` содержит:

- точные команды для проверки проекта;
- правила, какие файлы нельзя менять без подтверждения;
- описание generated/contract/shared модулей;
- внутренние библиотеки, starters и workflow/JMS особенности;
- mapping для Bitbucket MCP только там, где он точно известен.

## Рекомендуемый Workflow

Для Java/backend-разработки используйте `java-dev-assistant`:

```text
@java-dev-assistant почини обработку сообщения из очереди статусов
```

Он работает по тому же безопасному процессу: сначала классифицирует задачу, собирает локальный контекст, при необходимости делегирует точечный поиск исходников внутренних библиотек в `bitbucket-source-navigator`, составляет план и ждет подтверждения перед правками.

Для обычной frontend-разработки используйте `frontend-dev-assistant`:

```text
@frontend-dev-assistant добавить поиск в список инстансов
```

Ожидаемый процесс:

1. Классифицирует задачу как `bugfix`, `feature`, `test`, `refactor`, `review` или `explain`.
2. Собирает контекст проекта и релевантные docs.
3. Делегирует поиск кода агенту `code-navigator`.
4. Для нетривиальных багов делегирует расследование агенту `bugfix-investigator`.
5. Составляет план и ждет явного подтверждения.
6. После подтверждения меняет код только в утвержденном scope.
7. Вызывает `test-writer`, если изменилось поведение или нужны тесты.
8. Запускает проверки из проектных инструкций.
9. Вызывает `reviewer` перед финальным отчетом.
10. Возвращает, что изменено, почему и как проверено.

Когда изменения готовы к коммиту или MR, используйте `git-assistant`:

```text
@git-assistant подготовь коммит TEAMCVSI-123
```

Он использует только git context: `git status`, `git diff`, staged diff, branch/remote info и `git log`. Он не делает `git add`, `git commit`, `git push` и не создает Bitbucket MR без явного подтверждения. Commit message и MR title всегда должны быть в формате `TEAMCVSI-${number} Краткое сообщение на русском`.

## Модель Безопасности

- Агенты, которые могут менять код, сначала требуют план.
- `code-navigator`, `bugfix-investigator` и `reviewer` работают read-only.
- `bitbucket-source-navigator` работает read-only, использует Bitbucket MCP только при наличии точного evidence и не делает широкий поиск по репозиториям.
- `test-writer` может менять тесты, но не добавляет зависимости и тестовую инфраструктуру без подтверждения.
- `git-assistant` не читает исходники напрямую, не коммитит, не пушит и не создает Bitbucket MR без явного подтверждения.
- Skills подключены через allowlist для конкретных агентов, а не включены глобально.

## Заметки По Версии 0.1

Эта версия начиналась с frontend TypeScript/Vue/React-проектов и расширяется под Java/backend enterprise-разработку. Агенты должны адаптироваться к локальным правилам проекта через discovery репозитория.

Текущие ограничения:

- `test-writer` не добавляет новый тестовый стек, если его нет в проекте.
- `git-assistant` намеренно использует только git diff/status/branch/remote/log context и Bitbucket MCP для MR.
- `reviewer` сообщает о рисках, но не исправляет их.
- Агенты ожидают, что supporting subagents установлены под теми же именами.

## Первый Тестовый Запуск

В реальном проекте попробуйте:

```text
@frontend-dev-assistant объясни, как устроен основной frontend route flow
```

Затем попробуйте маленькое изменение поведения и проверьте, что агент сначала составит план, а не начнет сразу редактировать код.
