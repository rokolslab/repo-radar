# faster-whisper

> Upstream: https://github.com/SYSTRAN/faster-whisper  
> Категория: `speech-ai`  
> Теги: `whisper`, `ctranslate2`, `speech-to-text`, `cuda`, `quantization`, `python`  
> Статус: **К практическому тесту**  
> Последняя проверка: 2026-08-24

## Кратко

`faster-whisper` — высокопроизводительная реализация OpenAI Whisper на базе inference engine `CTranslate2`. Проект предназначен для локального распознавания речи и обычно используется как Python-библиотека или backend в сервисах транскрибации.

Главная идея — получить качество Whisper при существенно меньшем времени обработки и более эффективном использовании CPU/GPU-памяти. Upstream указывает ускорение до 4× относительно `openai/whisper` при сопоставимой точности; дополнительно поддерживаются FP16 и INT8-режимы, batching и GPU execution.

## Какую проблему решает

Оригинальный Whisper удобен как эталонная реализация, но для регулярной локальной транскрибации, пакетной обработки аудио или API-сервиса часто оказывается слишком тяжёлым.

`faster-whisper` заменяет PyTorch inference на CTranslate2:

```text
audio
  │
  ▼
PyAV decoder
  │
  ▼
Silero VAD (опционально)
  │
  ▼
Whisper model
  │
  ▼
CTranslate2
  │
  ├── CPU / INT8
  └── NVIDIA GPU / FP16 / INT8
  │
  ▼
segments / timestamps / text
```

## Основные возможности

- локальная транскрибация Whisper-моделями;
- CPU и NVIDIA GPU inference;
- FP16 и INT8 quantization;
- batched transcription;
- автоматическое определение языка;
- word-level timestamps;
- встроенный Silero VAD;
- поддержка Distil-Whisper;
- загрузка готовых CTranslate2-моделей с Hugging Face;
- конвертация совместимых Whisper/Transformers checkpoints;
- Python API, пригодный для встраивания в собственные приложения и сервисы.

## Производительность

В benchmark upstream для 13-минутного аудио на RTX 3070 Ti 8 GB модель `large-v2` показывала:

| Реализация | Режим | Время | VRAM |
|---|---|---:|---:|
| openai/whisper | FP16 | 2m23s | 4708 MB |
| faster-whisper | FP16 | 1m03s | 4525 MB |
| faster-whisper, batch=8 | FP16 | 17s | 6090 MB |
| faster-whisper | INT8 | 59s | 2926 MB |
| faster-whisper, batch=8 | INT8 | 16s | 4500 MB |

Это benchmark авторов и его нельзя автоматически переносить на другое железо, но он хорошо показывает назначение проекта: быстрый inference и эффективная работа с памятью.

## Требования

Базово:

- Python 3.9+;
- установка через `pip install faster-whisper`;
- отдельный системный FFmpeg не требуется: аудио декодируется через PyAV, который поставляет FFmpeg-библиотеки вместе с пакетом.

Для NVIDIA GPU текущие версии CTranslate2 требуют:

- CUDA 12;
- cuBLAS для CUDA 12;
- cuDNN 9.

При старых CUDA/cuDNN могут потребоваться предыдущие версии CTranslate2.

## Как можно использовать

### 1. Локальная расшифровка встреч и длинных аудиозаписей

Подходит для автоматической обработки:

- совещаний;
- лекций;
- вебинаров;
- интервью;
- голосовых заметок;
- технических записей.

Особенно полезен там, где аудио не должно уходить во внешние облачные API.

### 2. Backend собственного speech-to-text сервиса

`faster-whisper` удобно использовать как inference layer за FastAPI или другим HTTP-сервисом:

```text
client
  │
  ▼
REST / WebSocket API
  │
  ▼
faster-whisper
  │
  ▼
GPU / CPU
```

В экосистеме уже существуют готовые проекты, которые превращают его в OpenAI-compatible transcription API, например `speaches`.

### 3. Пакетная транскрибация

BatchedInferencePipeline позволяет эффективно загружать GPU при обработке больших объёмов аудио. Это интереснее обычного desktop-диктофона, когда нужно автоматически обработать десятки или сотни файлов.

### 4. Основа для diarization/alignment pipeline

Проект часто используется как нижний слой в более сложных решениях:

- WhisperX — diarization и точные word timestamps;
- whisper-diarization;
- WhisperLive;
- Whisper-Streaming;
- различные FastAPI/API wrappers.

То есть `faster-whisper` — скорее building block, чем законченная пользовательская программа.

## Практический сценарий для локальной GPU-машины

На ПК с NVIDIA GPU можно использовать `large-v3`, `turbo` или Distil-Whisper с GPU acceleration. Для RTX 5060 Ti 16 GB видеопамяти достаточно с большим запасом для стандартных Whisper-моделей; практический интерес будет не в том, «поместится ли модель», а в скорости, batching и выборе FP16/INT8 режима.

Пример базовой конфигурации:

```python
from faster_whisper import WhisperModel

model = WhisperModel(
    "large-v3",
    device="cuda",
    compute_type="float16",
)

segments, info = model.transcribe(
    "audio.mp3",
    vad_filter=True,
    word_timestamps=True,
)

for segment in segments:
    print(segment.start, segment.end, segment.text)
```

Для массовой обработки стоит отдельно тестировать `BatchedInferencePipeline`.

## Сильные стороны

1. Очень зрелая и популярная реализация Whisper.
2. Значительное ускорение относительно reference implementation.
3. Низкое потребление памяти в INT8-режиме.
4. CPU и GPU execution.
5. Batching.
6. Встроенный VAD.
7. Word timestamps.
8. Не требуется системный FFmpeg.
9. Большая экосистема готовых интеграций.
10. MIT license.

## Ограничения и риски

1. Это библиотека, а не готовое desktop-приложение.
2. Для NVIDIA GPU требуется аккуратно соблюдать совместимость CUDA/cuDNN/CTranslate2.
3. У upstream достаточно много открытых issues; перед production-внедрением стоит проверить конкретную платформу и версию.
4. Скорость сильно зависит от модели, beam size, batching и железа — benchmark нельзя переносить напрямую.
5. Diarization не входит в базовый faster-whisper и требует внешних решений.
6. Для truly streaming/low-latency режима обычно используют дополнительные проекты поверх faster-whisper.
7. Последний push в основной репозиторий на момент проверки был в ноябре 2025 года; проект не архивирован и остаётся широко используемым, но перед крупным внедрением стоит проверить текущую release/maintenance activity.

## Пересечения с другими проектами Repo Radar

### Handy

`Handy` — готовое desktop-приложение: hotkey → запись → локальная транскрибация → вставка текста в активное приложение.

`faster-whisper` — библиотека/inference backend.

```text
Handy
└── конечный пользовательский инструмент

faster-whisper
└── building block для собственных STT-систем
```

Они решают разные уровни задачи и не являются прямыми заменами.

### FreeToken

Оба проекта относятся к локальному AI inference, но работают с разными классами моделей:

- FreeToken — LLM/MoE text generation;
- faster-whisper — automatic speech recognition.

Вместе они позволяют строить полностью локальный голосовой AI pipeline:

```text
микрофон / аудио
       │
       ▼
faster-whisper
       │ text
       ▼
локальный агент / приложение
       │
       ▼
FreeToken / local LLM
```

## Зрелость проекта

На момент добавления в Repo Radar:

- создан в феврале 2023 года;
- около 25 тыс. stars;
- около 2 тыс. forks;
- основной язык — Python;
- лицензия — MIT;
- проект не архивирован;
- большая экосистема интеграций и downstream-проектов.

## Практический тест

1. Установить отдельное Python environment.
2. Проверить `large-v3` или `turbo` на CPU и GPU.
3. Прогнать один и тот же 30–60-минутный русский аудиофайл в FP16 и INT8.
4. Зафиксировать:
   - wall-clock time;
   - VRAM;
   - качество распознавания;
   - время загрузки модели;
   - эффект VAD.
5. Затем повторить тест с batching для нескольких файлов.
6. Если нужен API — отдельно сравнить готовый `speaches` с собственной FastAPI-обвязкой.

## Оценка

| Критерий | Оценка |
|---|---:|
| Функциональность | 5/5 |
| Зрелость | 5/5 |
| Производительность | 5/5 |
| Документация | 5/5 |
| Экосистема | 5/5 |
| Лицензия | 5/5 |
| Простота GPU deployment | 4/5 |
| Практическая ценность | 5/5 |

**Итог: 9/10.**

## Решение

**К практическому тесту.** Если требуется просто голосовой ввод в desktop-приложения — сначала смотреть Handy. Если требуется программная транскрибация, batch processing, API или собственный speech pipeline — `faster-whisper` является одним из основных кандидатов.
