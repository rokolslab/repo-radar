# eneskirca/nodeterm

> Upstream: https://github.com/eneskirca/nodeterm  
> Категория: `agent-workspaces`  
> Теги: `codex`, `opencode`, `claude-code`, `tmux`, `ssh`, `self-hosted`  
> Статус: **К практическому тесту**  
> Последняя проверка: 2026-08-21

## Кратко

Nodeterm — визуальное рабочее пространство для реальных терминальных сессий и coding agents. Терминалы и агенты представлены узлами на бесконечном canvas, а процессы работают через `tmux`, поэтому сессии переживают закрытие интерфейса и могут восстанавливаться после перезапуска.

Это не система автоматизации сети и не замена Ansible/NetBox. Его роль — верхний операторский слой: удобное рабочее место для нескольких параллельных Codex, Claude Code, OpenCode и других CLI-агентов.

## Основные возможности

- terminal nodes на pan/zoom canvas;
- persistent tmux sessions;
- агентные узлы для Claude Code, Codex, Gemini, GitHub Copilot, OpenCode, Grok и custom agents;
- sticky notes, Monaco editor, diff и web/video nodes;
- Kanban-представление живых агентных сессий;
- Git, worktrees и базовые source-control операции;
- удалённые проекты по SSH;
- desktop app для macOS/Linux;
- Server Edition для браузера;
- mobile companion;
- локальная голосовая диктовка через Whisper.

## Архитектура

Основной стек: Electron + React + xterm/node-pty + React Flow + tmux. Core-сервисы отделены от Electron через платформенный слой, поэтому тот же renderer используется и в desktop, и в Server Edition.

```text
Nodeterm UI
    │
    ├── local terminal / tmux
    ├── agent sessions
    ├── Git/worktrees
    └── SSH remote projects
             │
             ▼
      удалённые Linux-хосты
```

## Как можно использовать

### 1. Рабочее место для нескольких агентов

Держать несколько Codex/OpenCode/Claude Code сессий по разным проектам и визуально разделять их, вместо десятков терминальных вкладок.

### 2. Операторская консоль автоматизации

Nodeterm может находиться над Linux automation VM, где уже работают Ansible, Netmiko, NetBox, Git и специализированные scripts.

```text
                 Nodeterm
                    │
       ┌────────────┼────────────┐
       ▼            ▼            ▼
    Codex #1     Codex #2     OpenCode
       │            │            │
       └────────────┼────────────┘
                    ▼
            Automation Linux VM
              │      │      │
           Ansible NetBox Netmiko
```

### 3. Работа с удалёнными машинами

Проект можно открыть на удалённом хосте по SSH: терминалы, файлы и git работают там, а canvas остаётся локальным.

## Развертывание

Есть desktop-сборки и Server Edition. Для server deployment предусмотрен Node-based backend, WebSocket RPC и browser renderer. Безопасный рекомендуемый сценарий — bind на loopback и публикация через TLS reverse proxy.

Есть также headless notification mode без публичного listener.

## Сильные стороны

- реальная интеграция с tmux и терминалами, а не имитация agent dashboard;
- хороший fit для Codex/OpenCode workflows;
- SSH remote projects;
- worktrees;
- единый UI для нескольких живых сессий;
- Server Edition.

## Ограничения и риски

- проект молодой;
- не решает сам задачи infrastructure/network automation;
- лицензия **BUSL-1.1**, а не классическая open-source лицензия;
- production use разрешён с ограничениями на конкурирующие hosted/embedded/standalone продукты;
- до использования как основы собственного продукта лицензию нужно проверять отдельно.

## С чем пересекается

Частично пересекается с `aif-handoff` по проблеме параллельных агентных сессий и сохранения контекста, но решает её с другой стороны: терминалы, tmux, SSH и worktrees.

## Практический тест

Развернуть Server Edition на отдельной Ubuntu VM и проверить:

1. подключение из браузера;
2. запуск Codex и OpenCode;
3. persistence tmux после закрытия браузера;
4. SSH project на другой Linux-хост;
5. работу Git/worktrees;
6. удобство при 3–5 параллельных сессиях.

## Чек-лист

- [x] Первичный анализ
- [ ] Развернуть Server Edition
- [ ] Подключить Codex
- [ ] Подключить OpenCode
- [ ] Проверить SSH remote project
- [ ] Проверить ограничения BUSL для предполагаемого сценария
- [ ] Принять решение о постоянном использовании

## Итоговая оценка

**9/10** как рабочее место для coding agents.

## Решение

**К практическому тесту.**

## Источники

- https://github.com/eneskirca/nodeterm
- upstream `README.md`
- upstream `docs/SERVER.md`
- upstream `LICENSE`
