# Поток Spec-Driven Development (SDD)

## Обзор

SDD рассматривает specifications как первичный артефакт разработки. Код — это реализация «последней мили», полученная из хорошо определённых specs. Это позволяет:

- **Возобновляемость**: Продолжение работы между сессиями без потери контекста
- **Трассируемость**: Каждое решение по реализации восходит к requirements
- **Итеративность**: Уточнение specs перед написанием кода
- **Параллелизация**: Несколько агентов могут работать по одним specs

## Фазы потока

```
REQUIREMENTS → SPECIFICATIONS → PLAN → IMPLEMENTATION
     ↑              ↑            ↑
     └──────────────┴────────────┴── (итерация на любой фазе)
```

### Фаза 1: Requirements

**Input**: Пользователь описывает функцию/изменение которое хочет
**Output**: `flows/sdd-[name]/01-requirements.md`

Requirements фиксируют **что** и **зачем**:
- Решаемая проблема
- User stories (As a... I want... So that...)
- Acceptance criteria (Given/When/Then)
- Constraints и non-goals
- Открытые вопросы

### Фаза 2: Specifications

**Input**: Reviewed requirements
**Output**: `flows/sdd-[name]/02-specifications.md`

Specifications добавляют **как** на архитектурном уровне:
- Затронутые системы/компоненты
- Data models и interfaces
- Описания поведения
- Edge cases и error handling
- Dependencies и integration points

### Фаза 3: Plan

**Input**: Approved specifications
**Output**: `flows/sdd-[name]/03-plan.md`

Plan разбивает на actionable implementation:
- Разбивка задач с dependencies
- Изменения файлов (create/modify/delete)
- Стратегия тестирования
- Соображения rollback
- Оценочная сложность на задачу

### Фаза 4: Implementation

**Input**: Approved plan
**Output**: Working код + `flows/sdd-[name]/04-implementation-log.md`

Implementation выполняет план:
- Отслеживать прогресс по плану
- Документировать отклонения и почему
- Фиксировать learnings для уточнения spec

---

## Структура директорий

```
flows/
├── sdd.md                      # Этот файл (справка по потоку)
├── .templates/                 # Шаблоны для новых specs
│   ├── requirements.md
│   ├── specifications.md
│   ├── plan.md
│   └── implementation-log.md
└── sdd-[feature-name]/         # Директория specs на функцию
    ├── 01-requirements.md
    ├── 02-specifications.md
    ├── 03-plan.md
    ├── 04-implementation-log.md
    └── _status.md              # Текущая фаза + blockers
```

---

## Запуск нового потока SDD

### Новая функция
```
/sdd start [feature-name]
```

Создаёт `flows/sdd-[feature-name]/` с шаблонами и открывает фазу requirements.

### Возобновить существующий
```
/sdd resume [feature-name]
```

Читает `_status.md` для определения текущей фазы и продолжает оттуда.

### Fork (восстановление контекста)
```
/sdd fork [existing-name] [new-name]
```

Когда контекст потерян или pivot: создаёт новую директорию specs копируя существующие артефакты как стартовую точку, с `_status.md` замечающим fork.

---

## Переходы фаз

### Requirements → Specifications
- [ ] Requirements review пользователем
- [ ] Открытые вопросы разрешены
- [ ] Scope ясно ограничен
- [ ] Пользователь явно подтверждает: "requirements approved"

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

---

## Отслеживание статуса

Каждая директория specs имеет `_status.md`:

```markdown
# Status: sdd-[name]

## Current Phase
SPECIFICATIONS (awaiting review)

## Last Updated
2024-12-22 by Claude

## Blockers
- Waiting for Q clarify X

## Progress
- [x] Requirements drafted
- [x] Requirements approved
- [ ] Specifications drafted  ← current
- [ ] Specifications approved
- [ ] Plan drafted
- [ ] Plan approved
- [ ] Implementation started
- [ ] Implementation complete

## Context Notes
Ключевые решения или контекст для возобновления:
- Решили использовать X подход потому что Y
- Q предпочитает Z над W
```

---

## Протокол итерации

На любой фазе пользователь может запросить изменения:

1. **Minor adjustment**: Agent модифицирует текущий документ на месте
2. **Major pivot**: Fork в новую директорию specs с adjusted requirements
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

SDD следует принципам CLAUDE.md:
- **Явные предсказания**: Указывать ожидаемые outcomes перед implementation
- **Checkpoints**: Верифицировать после каждой задачи, не batch
- **Один test за раз**: Во время фазы implementation
- **Границы автономности**: Переходы фаз требуют подтверждения пользователя

---

## Анти-паттерны

- **Пропуск фаз**: Каждая фаза ловит разные классы ошибок
- **Расплывчатые requirements**: «Сделать лучше» → задавать уточняющие вопросы
- **Premature implementation**: Написание кода до approval plan
- **Silent pivots**: Изменение scope без обновления артефактов
- **Stale status**: Забывание обновить `_status.md`
