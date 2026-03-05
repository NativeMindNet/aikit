# Поток Tests-Driven Development (TDD)

## Обзор

TDD расширяет Document-Driven Development добавлением **фазы Tests** между Requirements и Specifications. Эта фаза определяет test cases в простом, читаемом формате перед написанием любой технической спецификации. Это обеспечивает tests направляют дизайн и implementation. Это позволяет:

- **Test-First мышление**: Определить success criteria перед деталями implementation
- **Ясные ожидания**: Все согласны как выглядит «done»
- **Влияние на дизайн**: Tests информируют архитектуру, не наоборот
- **Трассируемость**: Каждое requirement имеет соответствующие tests
- **Возобновляемость**: Test cases сохранены между сессиями

## Фазы потока

```
REQUIREMENTS → TESTS → SPECIFICATIONS → PLAN → IMPLEMENTATION → DOCUMENTATION
     ↑          ↑        ↑            ↑           ↑                ↑
     └──────────┴────────┴────────────┴───────────┴────────────────┘ (итерация на любой фазе)
```

### Фаза 1: Requirements

**Input**: Пользователь описывает функцию/изменение которое хочет
**Output**: `flows/tdd-[name]/01-requirements.md`

Requirements фиксируют **что** и **зачем**:
- Решаемая проблема
- User stories (As a... I want... So that...)
- Acceptance criteria (Given/When/Then)
- Constraints и non-goals
- Открытые вопросы

### Фаза 2: Tests (TDD-specific) — Cases-First мышление

**Input**: Approved requirements
**Output**: `flows/tdd-[name]/02-tests.md`

**Критично**: Эта фаза НЕ о написании test кода. Это об **исчерпывающем поведенческом анализе** — идентификации ВСЕХ cases которые определяют корректное поведение. Логика возникнет ИЗ этих cases.

#### Cases-First подход

```
1. ENUMERATE ALL BEHAVIORS
   - Happy paths (нормальная операция)
   - Edge cases (границы, лимиты)
   - Error cases (invalid input, failures)
   - Race conditions (concurrent сценарии)
   - State transitions (before/after)

2. DEFINE SUCCESS CRITERIA FOR EACH
   - Что точно должно произойти?
   - Какие state changes происходят?
   - Какие outputs произведены?

3. IDENTIFY COVERAGE GAPS
   - Какие behaviors untestable? Почему?
   - Какие требуют integration tests?
   - Какие требуют manual verification?

4. DESIGN ВОЗНИКАЕТ ИЗ CASES
   - Cases выявляют необходимые interfaces
   - Cases выявляют data structures
   - Cases выявляют error handling needs
```

#### Формат Test Case

```markdown
## Behavior: [Описательное имя behavior]

**Requirement**: [Linked requirement ID]
**Type**: unit | integration | e2e

### Case 1: [Конкретный сценарий]
**Given**: [Полное начальное состояние]
**When**: [Точное действие/event]
**Then**: [Точный ожидаемый outcome]

### Case 2: [Edge сценарий]
**Given**: [Edge condition состояние]
**When**: [Действие на границе]
**Then**: [Ожидаемое boundary поведение]

### Case 3: [Error сценарий]
**Given**: [Состояние которое вызовет error]
**When**: [Действие которое триггерит error]
**Then**: [Ожидаемое error handling]

### Выведенные Design Implications
- [Interface нужный для этого behavior]
- [Data structure implied by cases]
- [Error type нужный]
```

#### Чек-лист полноты

Перед утверждением фазы tests:
- [ ] Все requirements имеют соответствующие behaviors
- [ ] Все happy paths покрыты
- [ ] Все edge cases идентифицированы
- [ ] Все error scenarios определены
- [ ] State transitions задокументированы
- [ ] Integration points идентифицированы
- [ ] Design implications извлечены

### Фаза 3: Specifications — Выведено из Tests

**Input**: Approved test cases
**Output**: `flows/tdd-[name]/03-specifications.md`

**Критично**: Specifications ВЫВЕДЕНЫ из test cases, не изобретены независимо. Каждый элемент spec должен трассироваться к test cases которые валидируют его.

#### Процесс Derivation

```
Test Cases → Implied Interfaces → Specifications
Test Cases → Implied Data Structures → Specifications
Test Cases → Implied Error Handling → Specifications
```

#### Структура Specification

```markdown
## Interface: [Имя]

**Выведено из tests**: [Список ID test case]

### Methods/Functions
[Каждый метод существует потому что tests требуют его]

### Data Structures
[Каждая структура существует потому что tests оперируют на ней]

### Error Types
[Каждый error существует потому что tests ожидают его]

### Traceability Matrix
| Элемент Spec | Валидировано Tests |
|--------------|-------------------|
| method_a()   | test_1, test_2    |
| struct_b     | test_3, test_4    |
```

Specifications добавляют **как** на архитектурном уровне, спроектированы чтобы пройти tests:
- Затронутые системы/компоненты (implied by integration tests)
- Data models и interfaces (implied by test inputs/outputs)
- Описания поведения (прямо из test cases)
- Edge cases и error handling (из error test cases)
- Dependencies и integration points (из integration tests)
- **Каждый элемент spec трассируется к tests**

### Фаза 4: Plan

**Input**: Approved specifications
**Output**: `flows/tdd-[name]/04-plan.md`

Plan разбивает на actionable implementation:
- Разбивка задач с dependencies
- Изменения файлов (create/modify/delete)
- Стратегия тестирования (unit/integration/e2e)
- Соображения rollback
- Оценочная сложность на задачу

### Фаза 5: Implementation

**Input**: Approved plan
**Output**: Working код + `flows/tdd-[name]/05-implementation-log.md`

Implementation выполняет план:
- Отслеживать прогресс по плану
- Документировать отклонения и почему
- Фиксировать learnings для уточнения spec
- **Tests должны пройти** перед завершением

### Фаза 6: Documentation

**Input**: Завершённая implementation
**Output**: `flows/tdd-[name]/README.md`

Client-facing документация объясняет функцию простыми терминами:
- **Что делает**: Функциональность функции простым языком
- **Как работает**: Простое, нетехническое объяснение («на пальцах»)
- **Примеры использования**: Базовые примеры показывающие типичные use cases
- **Преимущества**: Ключевые преимущества для end user/client

---

## Структура директорий

```
flows/
├── tdd.md                      # Этот файл (справка по потоку)
├── .templates/
│   ├── requirements.md
│   ├── tests.md                # НОВЫЙ: Шаблон test cases
│   ├── specifications.md
│   ├── plan.md
│   ├── implementation-log.md
│   └── readme.md
└── tdd-[feature-name]/         # Директория документов на функцию
    ├── 01-requirements.md
    ├── 02-tests.md             # НОВЫЙ: Test cases
    ├── 03-specifications.md
    ├── 04-plan.md
    ├── 05-implementation-log.md
    ├── README.md
    └── _status.md              # Текущая фаза + blockers
```

---

## Запуск нового потока TDD

### Новая функция
```
/tdd start [feature-name]
```

Создаёт `flows/tdd-[feature-name]/` с шаблонами и открывает фазу requirements.

### Возобновить существующий
```
/tdd resume [feature-name]
```

Читает `_status.md` для определения текущей фазы и продолжает оттуда.

### Fork (восстановление контекста)
```
/tdd fork [existing-name] [new-name]
```

Когда контекст потерян или pivot: создаёт новую директорию документов копируя существующие артефакты как стартовую точку, с `_status.md` замечающим fork.

---

## Переходы фаз

### Requirements → Tests
- [ ] Requirements review пользователем
- [ ] Открытые вопросы разрешены
- [ ] Scope ясно ограничен
- [ ] Пользователь явно подтверждает: "requirements approved"

### Tests → Specifications
- [ ] Test cases review пользователем
- [ ] Все requirements имеют соответствующие tests
- [ ] Edge cases покрыты
- [ ] Пользователь явно подтверждает: "tests approved"

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
- [ ] Все tests passing
- [ ] Ready для client коммуникации
- [ ] Пользователь подтверждает фазу документации: "ready for docs"

---

## Отслеживание статуса

Каждая директория документов имеет `_status.md`:

```markdown
# Status: tdd-[name]

## Current Phase
TESTS (in progress)

## Last Updated
2024-12-22 by Claude

## Blockers
- None

## Progress
- [x] Requirements drafted
- [x] Requirements approved
- [x] Test cases drafted  ← current
- [ ] Tests approved
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
- Tests должны покрыть edge case где input пустой
- Пользователь хочет integration tests для API endpoints
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

TDD следует принципам CLAUDE.md:
- **Явные предсказания**: Указывать ожидаемые outcomes перед implementation
- **Checkpoints**: Верифицировать после каждой задачи, не batch
- **Один test за раз**: Во время фазы implementation
- **Границы автономности**: Переходы фаз требуют подтверждения пользователя

---

## Анти-паттерны

- **Пропуск фазы tests**: Побеждает цель TDD
- **Расплывчатые test cases**: «Должно работать» → определить конкретные ожидаемые outcomes
- **Tests после implementation**: Написание tests после кода побеждает TDD
- **Unmapped requirements**: Requirements без соответствующих tests
- **Premature implementation**: Написание кода до approval tests
- **Silent pivots**: Изменение scope без обновления артефактов
- **Stale status**: Забывание обновить `_status.md`
- **Игнорирование failing tests**: Implementation завершена но tests failing
