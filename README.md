# Repo Radar

Каталог GitHub-репозиториев, которые заслуживают изучения, тестирования или практического использования. Здесь хранится не пересказ оригинальных README, а прикладная оценка: что делает проект, где он полезен, как его можно встроить в рабочую среду, какие есть ограничения и стоит ли его пробовать.

## Каталог

### Agent Workspaces

| Репозиторий | Что это | Статус | Оценка |
|---|---|---|---:|
| [eneskirca/nodeterm](repos/agent-workspaces/nodeterm/README.md) | Визуальное рабочее пространство для терминалов и coding agents с tmux, SSH и Server Edition | К практическому тесту | 9/10 |

### Project Management

| Репозиторий | Что это | Статус | Оценка |
|---|---|---|---:|
| [usekaneo/kaneo](repos/project-management/kaneo/README.md) | Self-hosted project/task manager с GitHub-интеграцией и официальным MCP | Развернуть и проверить | 9/10 |

### Local LLM / Inference

| Репозиторий | Что это | Статус | Оценка |
|---|---|---|---:|
| [FlashML-org/FreeToken](repos/local-llm/freetoken/README.md) | Локальный inference engine для крупных MoE-моделей с совместным использованием GPU, RAM и CPU | R&D / практический тест | 9/10 |

### Agent Skills

| Репозиторий | Что это | Статус | Оценка |
|---|---|---|---:|
| [mattpocock/skills](repos/agent-skills/mattpocock-skills/README.md) | Набор composable engineering skills для Codex, Claude Code и других агентов | Внедрить одним из первых | 10/10 |
| [edvardgrishin27/nativeprompt](repos/agent-skills/nativeprompt/README.md) | Адаптация prompts под конкретный agent/model family по опубликованным правилам вендоров | К практическому тесту | 8/10 |

### Agent Learning

| Репозиторий | Что это | Статус | Оценка |
|---|---|---|---:|
| [mikeshu2001/agent-vmesto-chata](repos/agent-learning/agent-vmesto-chata/README.md) | Русскоязычный agent-guided курс по переходу от обычного чата к работе с Codex/Claude Code на реальной задаче | К практическому тесту | 8/10 |

### Infrastructure

| Репозиторий | Что это | Статус | Оценка |
|---|---|---|---:|
| [MHSanaei/3x-ui](repos/infrastructure/3x-ui/README.md) | Основная open-source панель управления Xray-core: клиенты, протоколы, multi-node, API, subscriptions и routing | Использовать / автоматизировать | 9/10 |
| [ServerTechnologies/3x-ui-with-xhttp](repos/infrastructure/3x-ui-with-xhttp/README.md) | Практический русскоязычный reference по VLESS + XHTTP через 3x-ui; tutorial, а не отдельный продукт | Изучить / reference | 7/10 |

### Utilities

| Репозиторий | Что это | Статус | Оценка |
|---|---|---|---:|
| [cjpais/Handy](repos/utilities/handy/README.md) | Полностью локальная cross-platform speech-to-text утилита: hotkey → диктовка → текст в активном приложении | К практическому тесту | 9/10 |

## Как устроен каталог

Каждый проект физически хранится только в одной основной категории. Дополнительные свойства — например `self-hosted`, `mcp`, `github-integration`, `local-llm` — фиксируются внутри его карточки, чтобы не создавать дубли.

```text
repo-radar/
├── README.md
├── repos/
│   ├── agent-workspaces/
│   ├── agent-skills/
│   ├── agent-learning/
│   ├── project-management/
│   ├── local-llm/
│   ├── infrastructure/
│   └── utilities/
├── docs/
│   ├── categories.md
│   └── evaluation-criteria.md
└── templates/
    └── repository-review.md
```

## Статусы

- **Изучить** — проект выглядит потенциально полезным, но нужен дополнительный анализ.
- **К практическому тесту** — есть достаточно оснований развернуть или установить проект.
- **Развернуть и проверить** — проект может заменить или существенно упростить уже планируемую разработку.
- **R&D** — технология перспективна, но ещё слишком молода или зависит от экспериментального стека.
- **Использовать** — практический тест подтвердил ценность.
- **Следить** — сейчас внедрение нецелесообразно, но проект стоит мониторить.
- **Отказаться** — выявлены ограничения, которые делают проект непригодным для текущих задач.

## Принцип оценки

Оценка учитывает функциональность, зрелость, активность разработки, лицензию, self-hosting, безопасность, документацию, API/интеграции, сложность внедрения и практическую ценность. Подробности: [docs/evaluation-criteria.md](docs/evaluation-criteria.md).

> Метаданные проектов быстро меняются. Даты, версии, stars/forks и совместимость следует перепроверять перед внедрением.