# Поток Visual-Driven Development (VDD)

## Обзор

VDD расширяет Document-Driven Development добавлением **фазы Visual** между Requirements и Specifications. Эта фаза использует простые ASCII представления для alignment на UI/UX, layouts и visual flows перед написанием любой технической спецификации. Это позволяет:

- **Visual Alignment**: Stakeholders могут «увидеть» функцию перед implementation
- **Ранний Feedback**: Поймать UX issues до написания кода
- **Простая коммуникация**: ASCII art универсален и не требует design tools
- **Трассируемость**: Visual mockups связывают requirements с technical specs
- **Возобновляемость**: Visual контекст сохранён между сессиями

## Фазы потока

```
REQUIREMENTS → VISUAL → SPECIFICATIONS → PLAN → IMPLEMENTATION → DOCUMENTATION
     ↑          ↑         ↑            ↑           ↑                ↑
     └──────────┴─────────┴────────────┴───────────┴────────────────┘ (итерация на любой фазе)
```

### Фаза 1: Requirements

**Input**: Пользователь описывает функцию/изменение которое хочет
**Output**: `flows/vdd-[name]/01-requirements.md`

Requirements фиксируют **что** и **зачем**:
- Решаемая проблема
- User stories (As a... I want... So that...)
- Acceptance criteria (Given/When/Then)
- Constraints и non-goals
- Открытые вопросы

### Фаза 2: Visual (VDD-specific)

**Input**: Approved requirements
**Output**: `flows/vdd-[name]/02-visual.md`

Фаза Visual создаёт **ASCII mockups** для alignment на внешнем виде:
- Screen layouts используя простые символы (`+`, `-`, `|`, `=`)
- Размещение и размер компонентов
- Navigation flows (экран → экран)
- Размещение кнопок/текста
- Форматы отображения данных (таблицы, списки, карточки)
- Представления состояний (empty, loading, error, success)

**ASCII Conventions:**
```
+------------------+     = Заголовок/Title
|   Title Here     |     - Divider/Separator
+------------------+     | Container edge
|  [Button]  (O)   |     [ ] Input field/button
|  Label: _____    |     (O) Radio/checkbox
|  * Required      |     * Required indicator
+------------------+     ~ Text area
| ~~~~~~~~~~~~~~~~ |
+------------------+
```

### Фаза 3: Specifications

**Input**: Approved visual mockups
**Output**: `flows/vdd-[name]/03-specifications.md`

Specifications добавляют **как** на архитектурном уровне, информированы visual layout:
- Затронутые системы/компоненты
- Data models и interfaces
- Описания поведения
- Edge cases и error handling
- Dependencies и integration points
- **Visual constraints** из approved mockups

### Фаза 4: Plan

**Input**: Approved specifications
**Output**: `flows/vdd-[name]/04-plan.md`

Plan разбивает на actionable implementation:
- Разбивка задач с dependencies
- Изменения файлов (create/modify/delete)
- Стратегия тестирования
- Соображения rollback
- Оценочная сложность на задачу

### Фаза 5: Implementation

**Input**: Approved plan
**Output**: Working код + `flows/vdd-[name]/05-implementation-log.md`

Implementation выполняет план:
- Отслеживать прогресс по плану
- Документировать отклонения и почему
- Фиксировать learnings для уточнения spec

### Фаза 6: Documentation

**Input**: Завершённая implementation
**Output**: `flows/vdd-[name]/README.md`

Client-facing документация объясняет функцию простыми терминами:
- **Что делает**: Функциональность функции простым языком
- **Как работает**: Простое, нетехническое объяснение («на пальцах»)
- **Примеры использования**: Базовые примеры показывающие типичные use cases
- **Преимущества**: Ключевые преимущества для end user/client

---

## Структура директорий

```
flows/
├── vdd.md                      # Этот файл (справка по потоку)
├── .templates/
│   ├── requirements.md
│   ├── visual.md               # НОВЫЙ: Шаблон ASCII mockup
│   ├── specifications.md
│   ├── plan.md
│   ├── implementation-log.md
│   └── readme.md
└── vdd-[feature-name]/         # Директория документов на функцию
    ├── 01-requirements.md
    ├── 02-visual.md            # НОВЫЙ: ASCII visual mockups
    ├── 03-specifications.md
    ├── 04-plan.md
    ├── 05-implementation-log.md
    ├── README.md
    └── _status.md              # Текущая фаза + blockers
```

---

## Запуск нового потока VDD

### Новая функция
```
/vdd start [feature-name]
```

Создаёт `flows/vdd-[feature-name]/` с шаблонами и открывает фазу requirements.

### Возобновить существующий
```
/vdd resume [feature-name]
```

Читает `_status.md` для определения текущей фазы и продолжает оттуда.

### Fork (восстановление контекста)
```
/vdd fork [existing-name] [new-name]
```

Когда контекст потерян или pivot: создаёт новую директорию документов копируя существующие артефакты как стартовую точку, с `_status.md` замечающим fork.

---

## Переходы фаз

### Requirements → Visual
- [ ] Requirements review пользователем
- [ ] Открытые вопросы разрешены
- [ ] Scope ясно ограничен
- [ ] Пользователь явно подтверждает: "requirements approved"

### Visual → Specifications
- [ ] ASCII mockups review пользователем
- [ ] Все экраны/состояния представлены
- [ ] Layout и navigation ясны
- [ ] Пользователь явно подтверждает: "visual approved"

### Specifications → Plan
- [ ] Specifications review пользователем
- [ ] Все затронутые системы идентифицированы
- [ ] Edge cases задокументированы
- [ ] Пользователь явно подтверждает: "specs approved"

### Plan → Implementation
- [ ] Plan review пользователем
- [ ] Задачи атомарные и testable
- [ ] Dependencies mapped
- [ ] Пользователь явно подтверждает: "plan approved"

### Implementation → Documentation
- [ ] Implementation завершена и протестирована
- [ ] Все запланированные функции working
- [ ] Ready для client коммуникации
- [ ] Пользователь подтверждает фазу документации: "ready for docs"

---

## Отслеживание статуса

Каждая директория документов имеет `_status.md`:

```markdown
# Status: vdd-[name]

## Current Phase
VISUAL (in progress)

## Last Updated
2024-12-22 by Claude

## Blockers
- None

## Progress
- [x] Requirements drafted
- [x] Requirements approved
- [x] Visual mockups drafted  ← current
- [ ] Visual approved
- [ ] Specifications drafted
- [ ] Specifications approved
- [ ] Plan drafted
- [ ] Plan approved
- [ ] Implementation started
- [ ] Implementation complete
- [ ] Documentation drafted
- [ ] Documentation approved

## Context Notes
Ключевые решения или контекст для возобновления:
- Пользователь предпочитает compact layout с sidebar navigation
- ASCII mockup approved для main screen, нужно добавить error states
```

---

## Протокол итерации

На любой фазе пользователь может запросить изменения:

1. **Minor adjustment**: Agent модифицирует текущий документ на месте
2. **Major pivot**: Fork в новую директорию документов с adjusted requirements
3. **Scope change**: Вернуться к фазе requirements

После каждой итерации:
- Обновить `_status.md` с что изменилось и почему
- Инкрементировать версию в заголовке документа если major изменение

---

## Передача сессии

При завершении сессии mid-flow:

1. Обновить `_status.md` с текущим состоянием
2. Задокументировать любое in-flight reasoning
3. Список явных следующих шагов
4. Заметить любой контекст который может быть потерян

Новая сессия читает `_status.md` первым для восстановления контекста.

---

## Интеграция с CLAUDE.md

VDD следует принципам CLAUDE.md:
- **Явные предсказания**: Указывать ожидаемые outcomes перед implementation
- **Checkpoints**: Верифицировать после каждой задачи, не batch
- **Один test за раз**: Во время фазы implementation
- **Границы автономности**: Переходы фаз требуют подтверждения пользователя

---

## Анти-паттерны

- **Пропуск фазы visual**: Побеждает цель VDD
- **Чрезмерно сложный ASCII**: Держать простым и читаемым
- **Расплывчатые requirements**: «Сделать красиво» → задавать уточняющие вопросы
- **Premature implementation**: Написание кода до approval visual
- **Silent pivots**: Изменение scope без обновления артефактов
- **Stale status**: Забывание обновить `_status.md`
- **Missing states**: Показ только happy path, не error/empty/loading состояний
- **Игнорирование approved visuals**: Implementation отклоняется от approved mockups
