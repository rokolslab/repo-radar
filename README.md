# Repo Radar

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Last commit](https://img.shields.io/github/last-commit/rokolslab/repo-radar)](https://github.com/rokolslab/repo-radar/commits/main)

> Curated radar of GitHub tools: practical reviews, use cases, risks, integrations, and adoption notes.

`Repo Radar` — прикладной каталог GitHub-репозиториев, которые заслуживают изучения, тестирования или использования. Здесь хранится не пересказ upstream README, а собственная оценка: какую проблему решает проект, как он устроен, где может пригодиться, как его встроить в рабочую среду, какие есть ограничения и стоит ли тратить время на практический тест.

Каждая карточка содержит единый паспорт проекта: upstream, основную категорию, теги, канонический статус и дату последней проверки. Подробные правила: [категории](docs/categories.md), [критерии оценки](docs/evaluation-criteria.md), [шаблон карточки](templates/repository-review.md).

## Каталог

### Agent Workspaces

| Репозиторий | Что это | Статус | Оценка |
|---|---|---|---:|
| [eneskirca/nodeterm](repos/agent-workspaces/nodeterm/README.md) | Визуальное рабочее пространство для терминалов и coding agents с tmux, SSH и Server Edition | К практическому тесту | 9/10 |

### Agent Skills

| Репозиторий | Что это | Статус | Оценка |
|---|---|---|---:|
| [mattpocock/skills](repos/agent-skills/mattpocock-skills/README.md) | Набор composable engineering skills для Codex, Claude Code и других агентов | К практическому тесту | 10/10 |
| [edvardgrishin27/nativeprompt](repos/agent-skills/nativeprompt/README.md) | Адаптация prompts под конкретный agent/model family по опубликованным правилам вендоров | К практическому тесту | 8/10 |

### Agent Learning

| Репозиторий | Что это | Статус | Оценка |
|---|---|---|---:|
| [mikeshu2001/agent-vmesto-chata](repos/agent-learning/agent-vmesto-chata/README.md) | Русскоязычный agent-guided курс по переходу от обычного чата к работе с Codex/Claude Code на реальной задаче | К практическому тесту | 8/10 |

### Local LLM / Inference

| Репозиторий | Что это | Статус | Оценка |
|---|---|---|---:|
| [FlashML-org/FreeToken](repos/local-llm/freetoken/README.md) | Локальный inference engine для крупных MoE-моделей с совместным использованием GPU, RAM и CPU | R&D | 9/10 |

### Speech AI

| Репозиторий | Что это | Статус | Оценка |
|---|---|---|---:|
| [SYSTRAN/faster-whisper](repos/speech-ai/faster-whisper/README.md) | Высокопроизводительный локальный Whisper backend на CTranslate2 для STT, batch processing и API-сервисов | К практическому тесту | 9/10 |

### Voice Agents

| Репозиторий | Что это | Статус | Оценка |
|---|---|---|---:|
| [videosdk-community/ai-telephony-demo](repos/voice-agents/ai-telephony-demo/README.md) | Reference/PoC AI-телефонии: self-hosted Python voice agent + VideoSDK SIP gateways + Gemini Live для inbound/outbound звонков | К практическому тесту | 8/10 |

### Project Management

| Репозиторий | Что это | Статус | Оценка |
|---|---|---|---:|
| [usekaneo/kaneo](repos/project-management/kaneo/README.md) | Self-hosted project/task manager с GitHub-интеграцией и официальным MCP | К практическому тесту | 9/10 |

### Infrastructure

| Репозиторий | Что это | Статус | Оценка |
|---|---|---|---:|
| [MHSanaei/3x-ui](repos/infrastructure/3x-ui/README.md) | Основная open-source панель управления Xray-core: клиенты, протоколы, multi-node, API, subscriptions и routing | Использовать | 9/10 |
| [ServerTechnologies/3x-ui-with-xhttp](repos/infrastructure/3x-ui-with-xhttp/README.md) | Практический русскоязычный reference по VLESS + XHTTP через 3x-ui; tutorial, а не отдельный продукт | Изучить | 7/10 |

### Utilities

| Репозиторий | Что это | Статус | Оценка |
|---|---|---|---:|
| [cjpais/Handy](repos/utilities/handy/README.md) | Полностью локальная cross-platform speech-to-text утилита: hotkey → диктовка → текст в активном приложении | К практическому тесту | 9/10 |
| [lasselian/prism-desktop](repos/utilities/prism-desktop/README.md) | Desktop dashboard для Home Assistant под Windows/Linux с tray, WebSocket sync, notifications и global hotkeys | К практическому тесту | 8/10 |

## Что находится в карточке

Для каждого проекта фиксируются:

- назначение и решаемая проблема;
- основные возможности и технический профиль;
- архитектура и deployment requirements;
- API, MCP, GitHub и другие интеграции;
- практические сценарии использования;
- сильные стороны, ограничения и риски;
- пересечения с другими инструментами;
- минимальный практический тест;
- итоговая оценка и решение.

## Статусы

В каталоге используется только один из шести статусов:

| Статус | Значение |
|---|---|
| **Изучить** | Проект выглядит полезным, но данных пока недостаточно для практического теста |
| **К практическому тесту** | Есть достаточно оснований установить или развернуть проект и проверить на реальной задаче |
| **R&D** | Перспективная, но экспериментальная или слишком молодая технология |
| **Использовать** | Проект достаточно зрелый и практически полезный для рассматриваемого сценария |
| **Следить** | Сейчас внедрение нецелесообразно, но проект стоит мониторить |
| **Отказаться** | Ограничения делают проект непригодным для рассматриваемого сценария |

Дополнительные рекомендации вроде «проверить первым», «автоматизировать» или «использовать как reference» пишутся внутри карточки и не создают новые статусы.

## Структура

Один проект физически хранится только в одной основной категории. Дополнительные свойства фиксируются тегами внутри карточки.

```text
repo-radar/
├── README.md
├── CONTRIBUTING.md
├── SECURITY.md
├── CODE_OF_CONDUCT.md
├── LICENSE
├── repos/
│   ├── agent-workspaces/
│   ├── agent-skills/
│   ├── agent-learning/
│   ├── local-llm/
│   ├── speech-ai/
│   ├── voice-agents/
│   ├── project-management/
│   ├── infrastructure/
│   └── utilities/
├── docs/
│   ├── categories.md
│   └── evaluation-criteria.md
├── templates/
│   └── repository-review.md
└── .github/
    ├── CODEOWNERS
    ├── ISSUE_TEMPLATE/
    └── PULL_REQUEST_TEMPLATE.md
```

## Как выставляется оценка

Оценка учитывает функциональность, зрелость, активность разработки, лицензию, self-hosting, безопасность, документацию, API/интеграции, сложность внедрения и практическую ценность. Итоговая оценка по шкале 1–10 не обязана быть простым средним арифметическим.

Подробнее: [docs/evaluation-criteria.md](docs/evaluation-criteria.md).

## Как предложить новый репозиторий

Самый простой вариант — создать issue через шаблон **Repository suggestion**. Для полноценной карточки используйте [templates/repository-review.md](templates/repository-review.md) и правила из [CONTRIBUTING.md](CONTRIBUTING.md).

Главный принцип: карточка должна отвечать на вопрос **«зачем и как это использовать?»**, а не дублировать документацию автора проекта.

## Достоверность и актуальность

Repo Radar — аналитический каталог, а не официальный источник документации рассматриваемых проектов. Перед установкой или внедрением необходимо перепроверять upstream-документацию, текущую лицензию, версии, требования, security notes и состояние проекта.

Stars, forks, версии, поддерживаемые платформы и другие быстро меняющиеся данные отражают состояние на дату последней проверки карточки и со временем могут устареть.

## Безопасность

Этот репозиторий не распространяет исполняемый код рассматриваемых проектов. Скрипты, контейнеры, installer-команды и конфигурации из внешних репозиториев следует проверять перед запуском. Подробнее: [SECURITY.md](SECURITY.md).

## Лицензия

Материалы Repo Radar распространяются по лицензии [MIT](LICENSE). Лицензии, торговые марки, код и документация внешних репозиториев принадлежат их соответствующим авторам и регулируются их собственными условиями.
