# videosdk-community/ai-telephony-demo

> Upstream: https://github.com/videosdk-community/ai-telephony-demo  
> Категория: `voice-agents`  
> Теги: `telephony`, `sip`, `voice-agent`, `gemini-live`, `videosdk`, `inbound-calls`, `outbound-calls`, `python`  
> Статус: **К практическому тесту**  
> Последняя проверка: 2026-08-24

## Кратко

`ai-telephony-demo` — небольшой reference-проект, показывающий, как подключить realtime AI voice agent к обычной телефонной сети через VideoSDK Telephony и SIP-провайдера.

Это не самостоятельная IP-PBX, SIP server или полностью self-hosted voice stack. Репозиторий демонстрирует интеграционный сценарий:

```text
телефонная сеть / PSTN
        │
        ▼
   SIP provider
        │
        ▼
VideoSDK Telephony
        │
        ├── inbound/outbound gateways
        ├── routing rules
        └── SIP call API
        │
        ▼
self-hosted Python agent worker
        │
        ▼
Gemini Live / realtime audio model
```

Главная практическая ценность — компактный пример полного пути от Python voice agent до реального входящего и исходящего телефонного звонка.

## Какую проблему решает

Создать голосового LLM-агента локально относительно просто. Сложнее подключить его к телефонной инфраструктуре:

- принять SIP/PSTN-звонок;
- направить звонок конкретному agent worker;
- передать двусторонний realtime audio;
- инициировать исходящий вызов;
- связать SIP trunk, routing и AI model в один pipeline.

Этот demo показывает один готовый вариант архитектуры, где телекоммуникационный слой берет на себя VideoSDK.

## Основные возможности

- входящие телефонные звонки;
- исходящие звонки через API;
- SIP inbound gateway;
- SIP outbound gateway;
- routing rule от телефонного gateway к self-hosted agent;
- realtime voice conversation;
- Gemini Live как voice-capable model;
- Python worker на базе `videosdk-agents`;
- регистрация agent worker в VideoSDK;
- ограничение количества параллельных процессов;
- пример TLS/media encryption для outbound gateway.

## Архитектура

### Inbound

```text
Caller
  │ PSTN
  ▼
Phone number / SIP provider
  │ SIP trunk
  ▼
VideoSDK Inbound Gateway
  │ routing rule
  ▼
Self-hosted Agent Worker
  │
  ▼
Gemini Realtime
  │
  ▼
voice response
```

### Outbound

```text
Application / script
       │
       │ POST /v2/sip/call
       ▼
VideoSDK SIP API
       │
       ▼
Outbound Gateway
       │ SIP trunk
       ▼
SIP provider
       │
       ▼
PSTN destination
```

## Код агента

Репозиторий минималистичен. Основная логика находится в одном `main.py`.

Используются:

- `videosdk-agents`;
- `videosdk-plugins-google`;
- `python-dotenv`;
- Gemini realtime model;
- `AgentSession` и `Pipeline` VideoSDK.

Agent задает простую инструкцию:

```text
You are a helpful AI assistant that answers phone calls.
Keep your responses concise and friendly.
```

При входе в session он произносит приветствие, при завершении — прощание.

## Realtime model

В текущей ветке `main-v4` используется:

```text
gemini-3.1-flash-live-preview
```

с audio response modality и voice `Leda`.

Это удобно для demo, потому что pipeline не требует отдельно собирать цепочку:

```text
STT → text LLM → TTS
```

Realtime multimodal model обрабатывает голосовой диалог как единую сессию.

При production-проектировании конкретную preview-модель и её SLA/availability следует перепроверять.

## Телефонная интеграция

### Inbound gateway

VideoSDK принимает звонок от SIP provider через inbound gateway. В README в качестве примера используется Twilio Elastic SIP Trunk, но архитектурно может применяться другой совместимый SIP-провайдер.

### Outbound gateway

Для исходящих звонков VideoSDK получает termination SIP URI провайдера. README рекомендует:

- transport: TLS;
- media encryption: enabled.

### Routing

Routing rule связывает gateway с self-hosted agent по `agent_id`.

Именно здесь в текущем upstream есть важное несоответствие.

README предлагает указать:

```text
agent1
```

но `main.py` фактически регистрирует:

```python
agent_id="MyTelephonyAgent"
```

При практическом тесте эти значения необходимо привести к одному идентификатору, иначе gateway не будет маршрутизировать звонок к ожидаемому worker.

## Исходящие звонки

Outbound call создаётся REST-запросом к VideoSDK:

```text
POST /v2/sip/call
```

В запрос передаются:

- VideoSDK JWT;
- `gatewayId`;
- номер назначения в `sipCallTo`.

Это делает demo пригодным не только для voice receptionist, но и для событийных outbound-сценариев: callback, уведомление, подтверждение заявки и т. п.

## Требования

Upstream указывает:

- Python 3.12+;
- VideoSDK account;
- `VIDEOSDK_API_KEY`;
- `VIDEOSDK_SECRET_KEY`;
- Google API key;
- SIP provider и телефонный номер для реальных PSTN-вызовов.

Установка Python-зависимостей:

```bash
pip install -r requirements.txt
```

Основные зависимости:

```text
videosdk-agents>=1.0.10
videosdk-plugins-google>=1.0.10
requests
python-dotenv
```

## Что является self-hosted, а что нет

Важно разделять компоненты:

```text
Self-hosted:
  └── Python agent worker

External services:
  ├── VideoSDK control/media/telephony layer
  ├── Google Gemini API
  └── SIP/PSTN provider
```

Поэтому проект нельзя считать полностью локальной или автономной AI-телефонией.

## Как можно использовать

### 1. AI receptionist

Входящий номер предприятия или тестовой линии направляется на voice agent:

```text
звонок
  ↓
AI receptionist
  ↓
ответ на типовые вопросы
  ↓
дальнейшая маршрутизация / действие
```

Для реального внедрения потребуется добавить business logic, transfer/escalation и интеграцию с внутренними системами.

### 2. Автоматический callback

После события в CRM/ERP/helpdesk приложение вызывает VideoSDK API и инициирует телефонный звонок пользователю.

### 3. Proof-of-concept AI-телефонии

Demo удобен, чтобы быстро проверить:

- задержку разговора;
- качество realtime voice model;
- SIP connectivity;
- поведение при входящих/исходящих вызовах;
- влияние телефонного кодека на распознавание речи.

### 4. Reference для собственной архитектуры

Даже если итоговое решение будет построено без VideoSDK, репозиторий полезен как простой reference архитектурных сущностей:

```text
SIP gateway
routing
worker registration
voice session
outbound call API
```

## Что потребуется добавить для реального продукта

Сам demo очень небольшой и не закрывает значительную часть production-задач.

Понадобятся, например:

- authentication/authorization для управляющего API;
- rate limiting;
- защита outbound calls от abuse;
- контроль стоимости звонков;
- retry и error handling;
- observability и call metrics;
- conversation logging policy;
- privacy/PII policy;
- управление consent для записи, если запись используется;
- transfer на человека;
- DTMF;
- business tools/functions;
- CRM/helpdesk/ERP integration;
- fallback при недоступности LLM;
- ограничения длительности звонка;
- тестирование concurrent calls;
- защита от prompt injection со стороны звонящего.

## Безопасность

Проект работает с несколькими критичными секретами:

- VideoSDK API key;
- VideoSDK secret key;
- Google API key;
- JWT для outbound calls;
- SIP provider credentials/configuration.

`.env` не должен попадать в Git.

Особенно опасен outbound API: компрометация credentials может привести не только к AI-расходам, но и к реальным платным телефонным вызовам.

Для production необходимо отдельно ограничивать:

- допустимые направления вызовов;
- частоту вызовов;
- максимальную длительность;
- budget/cost limits;
- доступ к call-trigger API.

## Масштабирование

В примерном `main.py` указано:

```python
max_processes=10
```

То есть demo уже показывает идею нескольких параллельных call workers, но это не является доказательством production capacity.

Перед реальным использованием необходимо измерять:

- concurrent calls;
- CPU/RAM одного worker;
- media latency;
- model latency;
- reconnect behavior;
- time-to-first-audio;
- failure recovery.

## Сильные стороны

1. Очень маленький и понятный пример.
2. Показывает полный inbound + outbound сценарий.
3. Использует реальные SIP trunks, а не только браузерный WebRTC demo.
4. Self-hosted worker позволяет держать собственную business logic у себя.
5. Realtime multimodal model упрощает voice pipeline.
6. MIT license.
7. Хорошая отправная точка для PoC.
8. Есть связанная документация VideoSDK Agent SDK и Telephony.

## Ограничения и риски

1. Это demo, а не production-ready telephony platform.
2. Сильная зависимость от облачной VideoSDK.
3. Зависимость от Google Gemini для текущей реализации.
4. Требуется внешний SIP/PSTN provider.
5. Текущий README и код расходятся по `agent_id`.
6. Используется preview realtime model, которую нужно перепроверять перед внедрением.
7. Нет готовой enterprise business logic, transfer, audit или CRM integration.
8. Не показана полноценная security hardening-модель.
9. Телефонные и LLM-вызовы создают несколько независимых статей затрат.

## Зрелость проекта

На момент проверки:

- создан: июнь 2025 года;
- около 357 stars;
- около 51 fork;
- основной язык: Python;
- лицензия: MIT;
- default branch: `main-v4`;
- проект не архивирован;
- upstream относится к VideoSDK community/examples ecosystem.

Это достаточно заметный reference project, но размер самого репозитория очень мал и его следует оценивать как demo, а не как зрелость полноценного продукта.

## Практический тест

1. Создать отдельный тестовый VideoSDK account/project.
2. Использовать тестовый SIP trunk и отдельный телефонный номер.
3. Исправить `agent_id`, чтобы routing rule и `main.py` совпадали.
4. Запустить worker локально.
5. Проверить inbound call.
6. Проверить outbound call через API.
7. Измерить:
   - answer latency;
   - first-response latency;
   - качество русского языка;
   - качество при телефонном codec;
   - interruptions/barge-in;
   - устойчивость 10–15 минут разговора.
8. Проверить два параллельных звонка.
9. Оценить реальную стоимость минуты: SIP + VideoSDK + Gemini.
10. После PoC решить, подходит ли vendor-dependent architecture или нужен более автономный SIP/voice stack.

## Чек-лист

- [x] Первичный анализ
- [ ] Исправить `agent_id` в тестовой конфигурации
- [ ] Поднять тестового worker
- [ ] Настроить SIP inbound gateway
- [ ] Проверить входящий звонок
- [ ] Настроить outbound gateway
- [ ] Проверить исходящий звонок
- [ ] Измерить latency и стоимость
- [ ] Проверить concurrent calls
- [ ] Оценить production gaps

## Итоговая оценка

**8/10** как практический reference/PoC для AI-телефонии.

Снижение относительно полноценных платформ связано с тем, что репозиторий является минимальным demo и требует нескольких внешних облачных сервисов.

## Решение

**К практическому тесту.** Репозиторий стоит использовать как быстрый способ проверить реальную AI-телефонию через SIP/PSTN и понять end-to-end latency и operational model. Для production архитектуру необходимо проектировать отдельно, не воспринимая demo как готовый эксплуатационный стек.

## Источники

- https://github.com/videosdk-community/ai-telephony-demo
- upstream `README.md`
- upstream `main.py`
- upstream `requirements.txt`
- VideoSDK Telephony documentation
- VideoSDK Agents SDK
