# FlashML-org/FreeToken

> Upstream: https://github.com/FlashML-org/FreeToken  
> Категория: `local-llm`  
> Теги: `inference`, `moe`, `cuda`, `nvidia`, `openai-api`, `anthropic-api`, `codex`, `opencode`  
> Статус: **R&D / практический тест**  
> Последняя проверка: 2026-08-21

## Кратко

FreeToken — локальный inference engine для запуска крупных open-weight Mixture-of-Experts моделей на consumer/workstation hardware. Его ключевая идея — использовать GPU VRAM, системную RAM, CPU и PCIe как единый ресурс, чтобы запускать модели, которые значительно больше объёма видеопамяти.

Это не сервис «бесплатных API-токенов». Название относится к локальному inference.

## Какую проблему решает

Обычный локальный inference быстро упирается в VRAM. FreeToken специально оптимизирует MoE-сценарий, где на каждом шаге активна только часть экспертов модели.

Эксперты могут храниться в host RAM, популярные — кешироваться в VRAM, а cache misses либо передаются по PCIe, либо вычисляются CPU.

## Основные возможности

- MoE inference на consumer NVIDIA GPUs;
- GPU expert cache;
- CPU/GPU co-execution;
- `fused`, `offload`, `cpu`, `hybrid`, `auto` backends;
- bandwidth calibration через `ft bench bw`;
- динамическое распределение VRAM между expert cache и KV cache;
- semantic-aware caching;
- OpenAI-compatible API;
- Anthropic-compatible API;
- launch integration для Codex, Claude Code, OpenCode и других coding agents;
- загрузка Hugging Face safetensors checkpoints;
- собственный fast weight format.

## Поддерживаемые модели

На момент проверки upstream перечисляет, в частности:

- DeepSeek-V4;
- GLM-5.2;
- GLM-4.7;
- Qwen3.6-35B-A3B;
- Qwen3.5-35B-A3B;
- Qwen3-30B-A3B;
- Qwen3.6-27B dense;
- OpenAI gpt-oss-120b;
- OpenAI gpt-oss-20b;
- Gemma-4;
- MiniMax-M2.5;
- Muse-Glimmer.

Список быстро меняется и должен перепроверяться перед тестом.

## Архитектура

```text
             MoE model
                 │
       ┌─────────┴─────────┐
       ▼                   ▼
   GPU / VRAM          Host RAM
       ▲                   │
       │       PCIe        │
       └───────────────────┘
                │
                ▼
               CPU
```

В `hybrid` режиме движок пытается совмещать перенос отсутствующих экспертов по PCIe и их вычисление на CPU.

## API и coding agents

FreeToken поднимает совместимые endpoints:

```text
/v1/chat/completions
/v1/responses
/v1/models
/v1/messages
/v1/messages/count_tokens
```

Это позволяет направлять совместимые приложения на локальный inference endpoint.

Есть специальная команда:

```bash
ft launch codex
ft launch claude
ft launch opencode
```

Для Codex создаётся отдельный профиль и model catalog, а не требуется вручную переписывать основной конфиг.

## Тестовая машина

Для практического теста доступна конфигурация:

- NVIDIA RTX 5060 Ti — 16 GB VRAM;
- 128 GB системной RAM;
- современный AMD CPU.

Это очень подходящий профиль для проверки FreeToken: 16 GB VRAM достаточно для небольшого active set/cache, а 128 GB RAM позволяют экспериментировать с гораздо более крупными MoE-моделями.

## Рекомендуемый порядок тестов

### Этап 1 — baseline

Начать с Qwen3.6-35B-A3B.

```bash
ft bench bw
ft serve --model Qwen/Qwen3.6-35B-A3B
```

Проверить API и затем:

```bash
ft launch codex
```

### Этап 2 — крупная модель

После стабильного baseline проверить `gpt-oss-120b`, чтобы оценить реальную пользу 128 GB RAM и offload/hybrid backend.

## Требования

На момент проверки CLI требует:

- Linux x86_64;
- NVIDIA GPU;
- driver R580+;
- CUDA 13;
- Python >= 3.10;
- `nvcc` для JIT compilation CUDA kernels.

Стек bleeding-edge: PyTorch/CUDA/Triton/FlashInfer/SGLang kernels, поэтому version compatibility особенно важна.

## Сильные стороны

- Apache-2.0;
- архитектура специально под большие MoE;
- использование RAM и CPU как части inference platform;
- OpenAI- и Anthropic-compatible API;
- готовая интеграция с Codex/OpenCode;
- потенциально позволяет получить существенно более крупную локальную модель без дорогой datacenter GPU.

## Ограничения и риски

- очень молодой проект;
- ранняя версия;
- независимых production-grade тестов пока мало;
- CUDA 13 и новый driver stack;
- производительность сильно зависит от CPU, RAM bandwidth и PCIe;
- large-model support не означает, что каждая огромная модель будет интерактивно быстрой;
- необходимы собственные benchmarks на конкретном железе.

## С чем пересекается

Конкурирует не с agent frameworks, а с inference runtimes: llama.cpp, vLLM, SGLang и похожими engines. Интересен именно там, где модель больше VRAM и хорошо подходит под MoE offload.

## Возможная схема

```text
Kaneo
  │ MCP
  ▼
Codex / OpenCode
  │
  ▼
FreeToken
  │
  ├── RTX 5060 Ti 16 GB
  ├── RAM 128 GB
  └── AMD CPU
```

## Чек-лист

- [x] Первичный анализ
- [ ] Установить подходящий Linux/CUDA stack
- [ ] Выполнить `ft bench bw`
- [ ] Запустить Qwen3.6-35B-A3B
- [ ] Измерить TTFT и tokens/s
- [ ] Подключить Codex
- [ ] Проверить tool-calling сценарий
- [ ] Проверить gpt-oss-120b
- [ ] Сравнить с llama.cpp/SGLang на том же железе

## Итоговая оценка

**9/10 как R&D-кандидат**, но зрелость пока ниже, чем у production inference engines.

## Решение

**Практически протестировать на RTX 5060 Ti 16 GB + 128 GB RAM.**

## Источники

- https://github.com/FlashML-org/FreeToken
- upstream `README.md`
- upstream `docs/install.md`
- upstream `docs/models.md`
- upstream `docs/quickstart.md`
- upstream `python/freetoken/launch.py`
