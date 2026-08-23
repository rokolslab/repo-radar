# Prism Desktop

> https://github.com/lasselian/prism-desktop

## Кратко

`Prism Desktop` — лёгкий desktop-клиент для Home Assistant под Windows и Linux. Он живёт в системном трее и даёт быстрый доступ к устройствам, автоматизациям, камерам и датчикам без необходимости держать открытой вкладку браузера.

Проект работает поверх Home Assistant WebSocket API, поэтому состояния сущностей обновляются в реальном времени. Для подключения используется URL экземпляра Home Assistant и long-lived access token.

## Какую проблему решает

Home Assistant отлично работает в браузере и через мобильные приложения, но на настольном ПК часто нужен более быстрый сценарий:

```text
горячая клавиша / tray
        │
        ▼
 Prism Desktop
        │ WebSocket API
        ▼
 Home Assistant
        │
        ├── lights
        ├── climate
        ├── cameras
        ├── sensors
        ├── scenes
        └── automations
```

Prism Desktop закрывает именно этот UX-слой: быстрый нативный dashboard на рабочем компьютере.

## Основные возможности

- системный tray;
- real-time sync через Home Assistant WebSocket API;
- desktop notifications;
- drag-and-drop dashboard;
- изменяемый размер панели;
- глобальная клавиша показа/скрытия приложения;
- горячие клавиши для отдельных сущностей;
- расширяемые controls для света и climate;
- поддержка камер;
- отдельный tile для 3D-принтера;
- настройка внешнего вида.

Поддерживаемые сущности включают `light`, `switch`, `climate`, `fan`, media, covers, vacuum, lawn mower, sensors, weather, camera, automation, scene и script.

## Технический профиль

На момент добавления в Repo Radar:

- основной язык: Python;
- лицензия: MIT;
- платформы: Windows и Linux;
- Windows: installer и portable `.exe`;
- Linux: AppImage;
- есть Nix flake;
- около 526 stars и 48 forks;
- репозиторий создан в феврале 2026 года и активно обновлялся летом 2026 года.

Проект пока заметно моложе Home Assistant и основных официальных клиентов, поэтому его лучше рассматривать как удобный дополнительный интерфейс, а не как критичный компонент домашней автоматизации.

## Аутентификация и безопасность

При первом запуске приложение запрашивает:

1. URL Home Assistant;
2. long-lived access token.

Это важный момент: такой токен фактически предоставляет приложению права пользователя, от имени которого он создан.

Рекомендации:

- не использовать токен администратора без необходимости;
- по возможности создать отдельного пользователя Home Assistant;
- хранить токен только на доверенном ПК;
- не публиковать конфигурацию Prism Desktop в Git;
- при компрометации ПК немедленно отозвать токен в Home Assistant.

## Как можно использовать

### 1. Быстрый Home Assistant dashboard на рабочем ПК

Самый очевидный сценарий: приложение постоянно работает в tray, а dashboard вызывается глобальным shortcut.

Это удобнее браузера, если Home Assistant используется много раз в течение рабочего дня.

### 2. Горячие клавиши для действий

Можно связать отдельные сущности с global shortcuts:

```text
hotkey
  │
  ▼
Prism Desktop
  │
  ▼
HA entity / automation
```

Например:

- включить рабочее освещение;
- запустить сцену;
- переключить розетку;
- вызвать automation;
- быстро открыть состояние камеры.

### 3. Desktop notifications

Home Assistant alerts могут появляться как стандартные уведомления Windows/Linux, что полезно для событий, которые человек должен увидеть, находясь за ПК.

### 4. Мониторинг 3D-принтера

Отдельный tile показывает камеру, температуры сопла и стола и состояние печати. Для домашней лаборатории это может быть удобнее отдельной вкладки HA.

## Windows

Для Windows проект выглядит особенно практично:

- есть installer;
- есть portable `.exe`;
- поддерживается автозапуск;
- tray и global hotkeys хорошо соответствуют типичному desktop workflow.

Именно Windows я бы выбрал как основную платформу для первого теста.

## Linux

Linux поддерживается через AppImage, но есть нюансы desktop environment.

### GNOME / Wayland

Для tray может понадобиться расширение AppIndicator. Глобальную клавишу показа приложения на Wayland предлагается задавать через системные настройки.

Per-entity shortcuts на GNOME Wayland не поддерживаются.

### KDE

Tray работает штатно, а app-toggle shortcut может использовать `org.freedesktop.portal.GlobalShortcuts`. Горячие клавиши отдельных сущностей также ограничены.

Таким образом, Windows сейчас выглядит более беспроблемной платформой для Prism Desktop.

## Развёртывание

### Windows

Скачать `PrismDesktopSetup.exe` либо portable executable из Releases.

### Linux

Используется AppImage. На Ubuntu может понадобиться `libfuse2`.

### Nix

Проект можно запускать непосредственно как flake:

```bash
nix run github:lasselian/prism-desktop
```

### Из исходников

```bash
git clone https://github.com/lasselian/prism-desktop.git
cd prism-desktop
pip install -r requirements.txt
python main.py
```

## Сильные стороны

1. Очень понятная задача: Home Assistant desktop UX.
2. Windows и Linux.
3. Real-time WebSocket API вместо периодического polling.
4. Tray + global shortcuts.
5. Desktop notifications.
6. Portable Windows build.
7. MIT license.
8. Не требует модификации Home Assistant server.

## Ограничения и риски

1. Молодой проект.
2. Использует long-lived access token, поэтому безопасность рабочего ПК критична.
3. Linux/Wayland имеет ограничения по global shortcuts.
4. Это сторонний клиент, а не официальный Home Assistant frontend.
5. Часть entity types и сложных dashboard-сценариев может быть реализована беднее, чем в Lovelace.
6. Не заменяет полноценную web-панель Home Assistant для администрирования и сложной настройки.

## Пересечения с другими инструментами Repo Radar

### Handy

Оба проекта являются локальными desktop utilities, которые уменьшают количество переключений между приложениями:

```text
Handy          → голос → любое текстовое поле
Prism Desktop  → hotkey → Home Assistant
```

### 3x-ui

Прямого функционального пересечения нет, но оба хорошо иллюстрируют модель «серверный backend + специализированный локальный UI».

### Agent tooling

Prism Desktop сам по себе не agent-инструмент. Но Home Assistant может быть отдельным контуром автоматизации, а coding agents — использоваться для разработки YAML, automations, integrations и инфраструктурной конфигурации вокруг него.

## Практический тест

1. Установить Windows build на отдельный ПК.
2. Создать отдельный long-lived token Home Assistant.
3. Добавить 5–10 часто используемых сущностей.
4. Настроить global shortcut для показа панели.
5. Назначить 2–3 горячие клавиши на реальные действия.
6. Проверить desktop notifications.
7. Оставить приложение в автозапуске на несколько дней и оценить стабильность WebSocket connection.
8. После теста решить, реально ли он быстрее привычного browser/mobile workflow.

## Оценка

| Критерий | Оценка |
|---|---:|
| Практическая полезность | 4/5 |
| Простота использования | 5/5 |
| Интеграция с Home Assistant | 4/5 |
| Кроссплатформенность | 4/5 |
| Зрелость | 3/5 |
| Безопасность модели подключения | 3/5 |
| Лицензия | 5/5 |
| Ценность для desktop workflow | 4/5 |

**Итог: 8/10.**

## Решение

**К практическому тесту.** Prism Desktop не меняет архитектуру Home Assistant, но может заметно улучшить ежедневное управление им с Windows/Linux ПК. Особенно интересен как tray-dashboard с глобальными горячими клавишами и desktop notifications.