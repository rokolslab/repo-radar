# mattpocock/skills

> Upstream: https://github.com/mattpocock/skills  
> Категория: `agent-skills`  
> Теги: `codex`, `claude-code`, `agent-skills`, `tdd`, `code-review`, `github-issues`, `workflow`  
> Статус: **К практическому тесту**  
> Последняя проверка: 2026-08-21

## Кратко

`mattpocock/skills` — набор небольших composable skills для coding agents. Это не MCP-сервер и не отдельное приложение, а готовые повторяемые инженерные процессы для Codex, Claude Code и других агентов.

Главная ценность — формализация нормальной software engineering дисциплины вокруг агента: проработка требований, domain modeling, спецификации, декомпозиция в вертикальные tickets, TDD, диагностика, code review и работа с большими инициативами через несколько agent sessions.

## Установка

Для Codex и других агентов upstream предлагает:

```bash
npx skills@latest add mattpocock/skills
```

Можно выбирать отдельные skills. После установки для конкретного репозитория рекомендуется выполнить `setup-matt-pocock-skills`.

## Основные skills

### Planning / requirements

- `grill-with-docs` — проработка задачи с фиксацией domain vocabulary и ADR;
- `domain-modeling` — формирование и уточнение domain model;
- `to-spec` — превращение текущего контекста в спецификацию;
- `to-tickets` — декомпозиция плана/spec в tracer-bullet tickets;
- `wayfinder` — планирование больших инициатив, которые не помещаются в одну agent session.

### Engineering

- `tdd` — red-green-refactor workflow;
- `diagnosing-bugs` — дисциплинированный цикл диагностики;
- `code-review` — независимая проверка Standards и Spec;
- `codebase-design` — проектирование глубоких модулей;
- `improve-codebase-architecture` — поиск архитектурных проблем;
- `resolving-merge-conflicts` — разрешение конфликтов по намерению изменений;
- `prototype` — быстрый throwaway prototype для проверки решения;
- `research` — исследование по первичным источникам;
- `wizard` — интерактивный CLI wizard для ручных инфраструктурных действий.

### Productivity

- `handoff` — компактная передача контекста следующему агенту/сессии;
- `grill-me` — интенсивное интервью по решению/плану;
- `teach`;
- `to-questionnaire`;
- `wait-what`.

## Почему `to-tickets` интересен

Skill требует не горизонтальной декомпозиции по слоям:

```text
DB → backend → frontend → tests
```

а tracer-bullet vertical slices, где каждый ticket даёт маленький, но проверяемый end-to-end результат.

Каждый ticket:

- должен помещаться в одну свежую agent context window;
- содержит blocking edges;
- должен быть отдельно проверяемым;
- может публиковаться в GitHub/другой tracker или локальные Markdown files.

Такой подход лучше подходит для агентной разработки, чем большие layer-by-layer задачи.

## Почему `wayfinder` интересен

`wayfinder` предназначен для инициатив, которые существенно больше одной сессии.

Он создаёт карту решений:

```text
Destination
    │
Wayfinder Map
    │
    ├── Research tickets
    ├── Prototype tickets
    ├── Grilling/decision tickets
    └── Task tickets
```

Важные понятия:

- **frontier** — открытые, незаблокированные и незанятые tickets;
- **blocking edges** — зависимости;
- **fog of war** — вопросы, которые ещё нельзя сформулировать достаточно точно;
- **decisions so far** — уже принятые решения;
- **out of scope** — сознательно исключённая работа.

Это полезная модель для долгоживущих проектов с несколькими Codex sessions.

## Code Review

`code-review` разделяет проверку на две независимые оси:

1. **Standards** — соответствие documented coding standards и baseline code smells;
2. **Spec** — соответствие реализации исходной спецификации/issue.

Обе проверки выполняются раздельно, чтобы хорошее качество кода не маскировало неверно реализованное требование и наоборот.

## Настройка репозитория

`setup-matt-pocock-skills` анализирует существующий repository и настраивает:

- issue tracker;
- triage labels;
- `CONTEXT.md` / domain docs;
- ADR layout;
- инструкции для `AGENTS.md` или `CLAUDE.md`.

GitHub является естественным вариантом, но предусмотрены GitLab, local Markdown и другие trackers.

## Как можно использовать

Рекомендуемый flow для реальной feature:

```text
grill-with-docs
      │
      ▼
   to-spec
      │
      ▼
  to-tickets
      │
      ▼
 Codex implementation
      │
      ▼
     tdd
      │
      ▼
 code-review
```

Для большой инициативы перед `to-spec` можно использовать `wayfinder`.

## Рекомендуемый стартовый набор

Не обязательно устанавливать всё. Практичный первый набор:

- `setup-matt-pocock-skills`;
- `grill-with-docs`;
- `domain-modeling`;
- `to-spec`;
- `to-tickets`;
- `wayfinder`;
- `tdd`;
- `diagnosing-bugs`;
- `code-review`;
- `handoff`;
- `wizard`.

## Ограничения и риски

Skills являются инструкциями агенту и могут содержать write-actions. Их нужно читать перед использованием.

Например upstream `implement` после реализации прямо предписывает выполнить commit в текущую branch. Для среды, где commit/push должны выполняться только по явной команде пользователя, этот skill лучше адаптировать.

Аналогично `to-tickets`, `triage` и `wayfinder` могут создавать/изменять Issues, labels, assignments и закрывать tickets. Это полезно, но требует осознанных GitHub permissions.

## С чем пересекается

Пересечение есть с ai-Factory и aif-handoff, но роли различаются:

```text
mattpocock/skills
    └── инженерная методология и workflow

Codex
    └── непосредственное выполнение работы

ai-Factory
    └── planning / roadmap

aif-handoff
    └── перенос состояния между sessions/agents
```

## Практический тест

Выбрать один активный repository и провести настоящую feature через полный flow:

1. установить выбранные skills;
2. выполнить setup;
3. `grill-with-docs`;
4. `to-spec`;
5. `to-tickets`;
6. реализация Codex;
7. `tdd`;
8. `code-review`;
9. сравнить качество и управляемость с обычной agent session.

До теста `implement` следует проверить и при необходимости изменить правило автоматического commit.

## Чек-лист

- [x] Первичный анализ
- [ ] Установить выбранные skills в тестовый repository
- [ ] Выполнить setup
- [ ] Провести одну реальную feature через полный workflow
- [ ] Адаптировать write/commit policies
- [ ] Сравнить с текущим процессом
- [ ] Решить, какие skills сделать стандартными

## Итоговая оценка

**10/10** как кандидат на быстрое практическое внедрение.

## Решение

**К практическому тесту.** Проверить одним из первых: минимальная инфраструктурная цена и высокая потенциальная отдача.

## Источники

- https://github.com/mattpocock/skills
- upstream `README.md`
- `setup-matt-pocock-skills/SKILL.md`
- `to-tickets/SKILL.md`
- `wayfinder/SKILL.md`
- `code-review/SKILL.md`
- `implement/SKILL.md`
