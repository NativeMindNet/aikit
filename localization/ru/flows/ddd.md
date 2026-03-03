# Поток Document-Driven Development (DDD)

## Обзор

DDD расширяет стандартную разработку фазой **коммуникации с заинтересованными лицами**. Используйте DDD когда функция значима настолько что требует объяснения клиентам, руководителям или end users. Финальная фаза Documentation создаёт **мини-презентацию** функции — не технические документы, а убедительное объяснение ценности.

**Когда использовать DDD (vs SDD):**
- Функция требует buy-in от клиента/stakeholder
- Нужно «продать» функцию decision-makers
- End users нужно понять что нового
- Marketing/sales нужно объяснение функции
- Документация является частью deliverable

DDD рассматривает документацию как первичный артефакт разработки, охватывающий requirements, specifications, implementation plans и client-facing презентацию. Код — это реализация «последней мили», полученная из хорошо определённых документов. Это позволяет:

- **Возобновляемость**: Продолжение работы между сессиями без потери контекста
- **Трассируемость**: Каждое решение по реализации восходит к requirements
- **Итеративность**: Уточнение документов перед написанием кода
- **Параллелизация**: Несколько агентов могут работать по одним документам
- **Buy-in от stakeholders**: Ясная, убедительная презентация функции для клиентов

## Фазы потока

```
REQUIREMENTS → SPECIFICATIONS → PLAN → IMPLEMENTATION → DOCUMENTATION
     ↑              ↑            ↑           ↑                ↑
     └──────────────┴────────────┴───────────┴────────────────┘ (итерация на любой фазе)
```

### Фаза 1: Requirements

**Input**: Пользователь описывает функцию/изменение которое хочет
**Output**: `flows/ddd-[name]/01-requirements.md`

Requirements фиксируют **что** и **зачем**:
- Решаемая проблема
- User stories (As a... I want... So that...)
- Acceptance criteria (Given/When/Then)
- Constraints и non-goals
- Открытые вопросы

### Фаза 2: Specifications

**Input**: Reviewed requirements
**Output**: `flows/ddd-[name]/02-specifications.md`

Specifications добавляют **как** на архитектурном уровне:
- Затронутые системы/компоненты
- Data models и interfaces
- Описания поведения
- Edge cases и error handling
- Dependencies и integration points

### Фаза 3: Plan

**Input**: Approved specifications
**Output**: `flows/ddd-[name]/03-plan.md`

Plan разбивает на actionable implementation:
- Разбивка задач с dependencies
- Изменения файлов (create/modify/delete)
- Стратегия тестирования
- Соображения rollback
- Оценочная сложность на задачу

### Фаза 4: Implementation

**Input**: Approved plan
**Output**: Working код + `flows/ddd-[name]/04-implementation-log.md`

Implementation выполняет план:
- Отслеживать прогресс по плану
- Документировать отклонения и почему
- Фиксировать learnings для уточнения spec

### Фаза 5: Documentation — Презентация функции

**Input**: Завершённая implementation
**Output**: `flows/ddd-[name]/README.md`

**Критично**: Это НЕ техническая документация. Это **мини-презентация** функции для stakeholders, клиентов или product marketing.

#### Mindset коммуникации со Stakeholder

```
Думать: «Как объяснить это...»
- Клиенту который заплатит за эту функцию?
- Руководителю которому нужно approve бюджет?
- Пользователю который получит выгоду от этого?
- Sales команде которая будет pitch это?
```

#### README.md как презентация функции

```markdown
# [Имя функции] — [Ценностное предложение в одну строку]

## Проблема
[Болевая точка которую решает — язык stakeholders, не технический]

## Решение
[Как эта функция адресует проблему — фокус на преимуществах]

## Ключевые преимущества
- [Преимущество 1 — бизнес/пользовательская ценность]
- [Преимущество 2 — конкурентное преимущество]
- [Преимущество 3 — gain эффективности]

## Как это работает (просто)
[Аналогия или простое объяснение — «на пальцах»]
[Нет технического жаргона — ваша бабушка должна понять]

## Пример сценария
[Конкретная история: «Представьте вы... и вам нужно...»]

## Начало работы
[Простейший путь к ценности — максимум 3 шага]

## FAQ
[Ожидаемые вопросы stakeholders]
```

#### Тон и стиль

- **Value-first**: Вести с преимуществ, не с функций
- **Без жаргона**: Нет технических терминов без объяснения
- **Конкретно**: Использовать примеры, сценарии, аналогии
- **Scannable**: Заголовки, bullets, короткие параграфы
- **Убедительно**: Почему им должно быть важно?

Эта фаза связывает техническую реализацию с пониманием и buy-in от stakeholders.

---

## Структура директорий

```
flows/
├── ddd.md                      # Этот файл (справка по потоку)
├── .templates/                 # Шаблоны для новых документов
│   ├── requirements.md
│   ├── specifications.md
│   ├── plan.md
│   ├── implementation-log.md
│   └── readme.md               # НОВЫЙ: Шаблон client документации
└── ddd-[feature-name]/         # Директория документов на функцию
    ├── 01-requirements.md
    ├── 02-specifications.md
    ├── 03-plan.md
    ├── 04-implementation-log.md
    ├── README.md               # НОВЫЙ: Client-facing документация
    └── _status.md              # Текущая фаза + blockers
```

---

## Запуск нового потока DDD

### Новая функция
```
/ddd start [feature-name]
```

Создаёт `flows/ddd-[feature-name]/` с шаблонами и открывает фазу requirements.

### Возобновить существующий
```
/ddd resume [feature-name]
```

Читает `_status.md` для определения текущей фазы и продолжает оттуда.

### Fork (восстановление контекста)
```
/ddd fork [existing-name] [new-name]
```

Когда контекст потерян или pivot: создаёт новую директорию документов копируя существующие артефакты как стартовую точку, с `_status.md` замечающим fork.

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

### Implementation → Documentation
- [ ] Implementation завершена и протестирована
- [ ] Все запланированные функции working
- [ ] Ready для client коммуникации
- [ ] Пользователь подтверждает фазу документации: "ready for docs"

---

## Отслеживание статуса

Каждая директория документов имеет `_status.md`:

```markdown
# Status: ddd-[name]

## Current Phase
DOCUMENTATION (in progress)

## Last Updated
2024-12-22 by Claude

## Blockers
- None

## Progress
- [x] Requirements drafted
- [x] Requirements approved
- [x] Specifications drafted
- [x] Specifications approved
- [x] Plan drafted
- [x] Plan approved
- [x] Implementation started
- [x] Implementation complete
- [ ] Documentation drafted  ← current
- [ ] Documentation approved

## Context Notes
Ключевые решения или контекст для возобновления:
- Решили использовать X подход потому что Y
- Пользователь предпочитает Z над W
- Клиенту нужны простые примеры без технического жаргона
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

DDD следует принципам CLAUDE.md:
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
- **Missing client documentation**: Пропуск фазы README оставляет клиентов без понимания
- **Технический жаргон в README**: Использование сложных терминов вместо простых объяснений
