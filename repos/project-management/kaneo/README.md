# usekaneo/kaneo

> Upstream: https://github.com/usekaneo/kaneo  
> Категория: `project-management`  
> Теги: `self-hosted`, `project-management`, `kanban`, `mcp`, `github`, `postgresql`  
> Статус: **К практическому тесту**  
> Последняя проверка: 2026-08-21

## Кратко

Kaneo — self-hosted система управления проектами, задачами и issues, ориентированная на более простой workflow, чем Jira/Linear. Проект особенно интересен тем, что объединяет обычный project/task management с официальным MCP и двусторонней GitHub-интеграцией.

Для задач личного и технического project tracking Kaneo следует проверить до разработки собственного полноценного backend трекера: значительная часть базовых функций уже реализована.

## Основные возможности

- workspaces и проекты;
- Kanban/status columns;
- задачи, описание, приоритеты, исполнители, start/due dates;
- комментарии и activity history;
- labels;
- task relations: `subtask`, `blocks`, `related`;
- time entries;
- роли и permissions;
- realtime updates через events/WebSockets;
- PostgreSQL;
- Redis опционально для multi-instance realtime;
- Docker Compose;
- Helm chart;
- официальный MCP;
- GitHub и Gitea integrations.

## Архитектура

```text
React / Vite
     │
     ▼
Hono API
     │
     ├── Better Auth / permissions
     ├── Events / WebSockets
     ├── MCP
     ├── GitHub integration
     │
     ▼
PostgreSQL
     │
Redis (опционально)
```

API является authority для authentication/authorization и бизнес-логики. UI не должен быть единственным уровнем проверки прав.

## MCP

Каждая инсталляция имеет встроенный Streamable HTTP endpoint:

```text
https://<kaneo-host>/api/mcp
```

Для MCP используется OAuth 2.1 с PKCE. Есть также официальный stdio package `@kaneo/mcp`.

Через MCP агент может работать с:

- workspaces;
- projects;
- tasks;
- statuses;
- assignees;
- due dates;
- comments;
- labels;
- time entries;
- task relations;
- notifications и search.

Это делает Kaneo особенно удобным source of truth для Codex/Claude/OpenCode workflows.

## GitHub-интеграция

Kaneo использует GitHub App и поддерживает двустороннюю синхронизацию.

### Kaneo → GitHub

- задача → Issue;
- изменение статуса/priority → Issue/labels;
- комментарии;
- close/reopen issue.

### GitHub → Kaneo

- Issue → task;
- labels → task properties;
- PR → связь с задачей;
- branch push → `in-progress`;
- PR opened → `in-review`;
- PR merged → `done`.

Это потенциально позволяет связать project tracking непосредственно с агентной разработкой.

## Как можно использовать

### 1. Единый источник проектов и задач

Kaneo хранит проекты и задачи, а Codex работает с ними через MCP.

```text
Codex / OpenCode
       │ MCP
       ▼
     Kaneo
       │
       ├── Projects
       ├── Tasks
       └── Relations
       │
       ▼
     GitHub
```

### 2. Основа вместо собственного generic tracker backend

Специфическую логику — например AI scoring, Top-3 focus, дополнительные reminders — можно строить поверх Kaneo, не реализуя заново users, tasks, Kanban, permissions, comments, relations, PostgreSQL и GitHub sync.

## Развертывание

Минимальная инсталляция: контейнер Kaneo + PostgreSQL. Redis для single-instance deployment не обязателен. Есть Docker Compose и Helm.

Для GitHub webhook integration экземпляр должен быть доступен GitHub по HTTPS.

## Сильные стороны

- MIT license;
- полноценный self-hosting;
- официальный MCP, а не сторонний adapter;
- сильная GitHub-интеграция;
- task dependencies/relations;
- PostgreSQL и нормальная authorization model;
- активная разработка;
- относительно простой single-instance deployment.

## Ограничения и риски

- нужно проверить качество UX на реальном объёме проектов;
- специфическая логика личной приоритизации не является основной задачей Kaneo;
- reminders и нестандартные состояния вроде `frozen until date` могут потребовать внешней автоматизации;
- GitHub App/Webhooks требуют аккуратной настройки секретов и public HTTPS endpoint;
- перед production deployment нужна отдельная security review.

## С чем пересекается

Сильно пересекается с идеей FolioBeacon как generic project/task tracker. Возможно, рациональнее оставить в собственной разработке только специализированный AI/automation слой, используя Kaneo как source of truth.

## Практический тест

1. Развернуть Kaneo через Docker Compose.
2. Создать тестовый workspace и несколько проектов.
3. Подключить встроенный MCP к Codex.
4. Проверить `list/create/update/move task`.
5. Проверить `blocks`, `subtask`, `related`.
6. Создать GitHub App и подключить один тестовый repository.
7. Проверить Issue ↔ Task и PR/status automation.
8. Оценить, какие функции FolioBeacon действительно остаются уникальными.

## Чек-лист

- [x] Первичный анализ
- [ ] Развернуть Docker Compose
- [ ] Подключить MCP к Codex
- [ ] Проверить task relations
- [ ] Проверить GitHub App integration
- [ ] Сравнить с требованиями FolioBeacon
- [ ] Принять архитектурное решение

## Итоговая оценка

**9/10** для self-hosted project management с agent integration.

## Решение

**К практическому тесту.** Развернуть и проверить до дальнейшей разработки собственного generic project tracker.

## Источники

- https://github.com/usekaneo/kaneo
- upstream `README.md`
- upstream `AGENTS.md`
- upstream MCP documentation
- upstream GitHub integration documentation
