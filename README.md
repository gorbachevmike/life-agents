# Life Agents

Переиспользуемые OpenCode-агенты и skills для frontend-разработки.

Версия: `0.1`

## Что Внутри

Агенты находятся в `agents/`:

- `frontend-dev-assistant` - основной агент для frontend-задач. Классифицирует задачу, собирает контекст, составляет план до правок, делегирует специализированным агентам, запускает проверки и возвращает отчет.
- `code-navigator` - read-only subagent для навигации по коду. Находит релевантные файлы, точки входа, execution path, связи и похожие реализации.
- `bugfix-investigator` - read-only subagent для расследования багов. Воспроизводит или анализирует симптом, строит гипотезы и предлагает минимальный фикс.
- `test-writer` - subagent для тестов. Находит тестовый стек, выбирает минимально полезную testing seam, пишет behavior tests и запускает только релевантные тесты.
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
5. Составляет план и ждет явного подтверждения.
6. После подтверждения меняет код только в утвержденном scope.
7. Вызывает `test-writer`, если изменилось поведение или нужны тесты.
8. Запускает проверки из проектных инструкций.
9. Вызывает `reviewer` перед финальным отчетом.
10. Возвращает, что изменено, почему и как проверено.

Когда изменения готовы к коммиту, используйте `commit-assistant`:

```text
@commit-assistant подготовь коммит
```

Он использует только git context: `git status`, `git diff`, staged diff и свежий `git log`. Он не делает `git add` и `git commit` без явного подтверждения.

## Модель Безопасности

- Агенты, которые могут менять код, сначала требуют план.
- `code-navigator`, `bugfix-investigator` и `reviewer` работают read-only.
- `test-writer` может менять тесты, но не добавляет зависимости и тестовую инфраструктуру без подтверждения.
- `commit-assistant` не читает исходники напрямую и не коммитит без явного подтверждения.
- Skills подключены через allowlist для конкретных агентов, а не включены глобально.

## Заметки По Версии 0.1

Эта версия ориентирована на frontend TypeScript/Vue/React-проекты, но агенты должны адаптироваться к локальным правилам проекта через discovery репозитория.

Текущие ограничения:

- `test-writer` не добавляет новый тестовый стек, если его нет в проекте.
- `commit-assistant` намеренно использует только git diff/status/log context.
- `reviewer` сообщает о рисках, но не исправляет их.
- Агенты ожидают, что supporting subagents установлены под теми же именами.

## Первый Тестовый Запуск

В реальном проекте попробуйте:

```text
@frontend-dev-assistant объясни, как устроен основной frontend route flow
```

Затем попробуйте маленькое изменение поведения и проверьте, что агент сначала составит план, а не начнет сразу редактировать код.
