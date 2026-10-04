# darrenhinde/OpenAgentsControl

> Upstream: https://github.com/darrenhinde/OpenAgentsControl\
> Категория: `agent-systems`\
> Теги: `opencode`, `coding-agents`, `project-context`, `approval-gates`, `workflow`\
> Статус: **К практическому тесту**\
> Последняя проверка: 2026-10-04\
> Проверенный commit: `37ca233fa5597a5abb90cba73165deafffe0344f` (`main`)

## Кратко

OpenAgentsControl (OAC) — набор агентов, контекстных файлов и процессов разработки поверх OpenCode. Upstream также описывает Claude Code plugin в статусе beta. Основная задача — дать coding agents правила проекта и точки согласования перед реализацией; это не UI для терминальных сессий. Установка и поведение gates здесь не проверялись.

## Какую проблему решает

Без явного контекста агент может генерировать код, который не соответствует соглашениям проекта. OAC предлагает хранить образцы и правила в репозитории, подгружать их перед работой и проводить задачу через план, согласование, реализацию и проверку.

## Основные возможности

- хранение project patterns и контекста в редактируемых файлах;
- специализированные агенты для поиска контекста, реализации, тестирования и review;
- документированные approval gates в workflow;
- профили установки и обновления;
- отдельный beta plugin для Claude Code.

Гарантии «всегда спрашивает разрешение» и «production-ready результат» — заявления авторов, не подтверждённые нашим тестом.

## Архитектура

README описывает OAC как слой поверх OpenCode: локальные context-файлы и agent definitions управляют поведением, а OpenCode предоставляет runtime и модель. `package.json` описывает пакет и evaluation tooling; это не доказательство, что каждый workflow прошёл тест на выбранной машине. Beta plugin для Claude Code — отдельный путь использования, который нельзя смешивать с основным OpenCode setup.

## Как можно использовать

На небольшом учебном репозитории задать два конкретных code patterns и попросить агента реализовать одну ограниченную функцию. Проверить, какие файлы контекста были прочитаны, когда появляется запрос на согласование и действительно ли тесты/review выполняются, а не только упоминаются в ответе.

## Интеграции

- OpenCode CLI — основной runtime;
- Claude Code plugin — beta-путь по README;
- выбранная через runtime LLM-модель;
- Git и локальные файлы проекта для shared context.

Наличие конкретного API, MCP endpoint или универсальной поддержки других CLI для OAC на выбранном commit не подтверждено; здесь они не заявляются.

## Развертывание и требования

README требует OpenCode CLI, Bash 3.2+ и Git. Документация описывает macOS, Linux и Windows через Git Bash/WSL; для некоторых сценариев упоминает `curl` и `jq`. Upstream предлагает удалённый shell installer, но мы его не запускали. Перед тестом нужно изучить `install.sh` и `update.sh`, зафиксировать commit и применить только в одноразовом репозитории без credentials.

## Сильные стороны

- явная идея project patterns и reviewable context-файлов;
- разделение планирования, согласования и исполнения в документации;
- есть инструкции по установке и платформам;
- файл `LICENSE` содержит текст MIT.

## Ограничения и риски

- approval gates описаны автором, но фактическое исполнение при сбое или обходном пути не проверено;
- installer и update script могут менять agent-конфигурацию; нужен file-level diff и rollback;
- отдельный Claude Code plugin обозначен beta; его нельзя считать равным зрелости основного сценария;
- сравнения скорости и качества с другими tools в README являются маркетинговыми заявлениями, а не независимыми измерениями;
- не проверены current security advisories, поведение на конкретной версии OpenCode и полнота тестов.

## С чем пересекается

С `ai-factory` пересекается как средство стандартизации agent development, но OAC прежде всего задаёт OpenCode-oriented patterns и gates; AI Factory устанавливает skills и конфигурацию для нескольких agent runtimes. С `agent-workspaces/nodeterm` пересечение ограничено работой с агентами: Nodeterm — интерфейс сессий, OAC — правила их поведения.

## Практический тест

В чистом тестовом Git-репозитории проверить installer source и ожидаемые записи, выполнить локальную установку, задать один pattern и одну задачу. Зафиксировать момент согласования до первой записи, проверить generated diff, тест/review artifacts и реакцию на отказ в согласовании. Затем удалить установленные файлы по документированному пути и убедиться, что исходный проект восстановлен.

## Чек-лист

- [x] Прочитаны README, installation/platform guides, `package.json` и MIT `LICENSE` на указанном commit
- [ ] Проверены installer/update scripts и права записи
- [ ] Развернуто в изолированном проекте
- [ ] Проверены approval gates, тестирование и rollback на реальном сценарии
- [ ] Проверены текущие security advisories и версия OpenCode
- [ ] Принято решение о применении

## Итоговая оценка

**6/10 — предварительная оценка по документации.** Плюсы: конкретный сценарий для project patterns, описанные approval gates, MIT license и руководство по платформам. Сдерживающие факторы: ключевые гарантии gates не проверены, удалённый shell installer требует отдельного review, основной путь привязан к OpenCode, альтернативный plugin обозначен beta. Балл отражает перспективность, а не подтверждённое качество исполнения.

## Решение

**К практическому тесту.** После review installer провести короткий изолированный сценарий с обязательным отказом и разрешением перед рассмотрением постоянного использования.

## Источники

- [README](https://github.com/darrenhinde/OpenAgentsControl/blob/37ca233fa5597a5abb90cba73165deafffe0344f/README.md)
- [LICENSE](https://github.com/darrenhinde/OpenAgentsControl/blob/37ca233fa5597a5abb90cba73165deafffe0344f/LICENSE)
- [package.json](https://github.com/darrenhinde/OpenAgentsControl/blob/37ca233fa5597a5abb90cba73165deafffe0344f/package.json)
- [Installation guide](https://github.com/darrenhinde/OpenAgentsControl/blob/37ca233fa5597a5abb90cba73165deafffe0344f/docs/getting-started/installation.md)
- [Platform compatibility](https://github.com/darrenhinde/OpenAgentsControl/blob/37ca233fa5597a5abb90cba73165deafffe0344f/docs/getting-started/platform-compatibility.md)
