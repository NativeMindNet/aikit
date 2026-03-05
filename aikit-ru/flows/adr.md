# Поток Architecture Decision Records (ADR)

## Обзор

ADR (Architecture Decision Records) — это структурированный подход к документированию значимых архитектурных и технических решений. Каждый ADR фиксирует контекст, рассмотренные варианты, rationale решения и последствия — создавая трассируемую историю того, как эволюционировала система.

Этот поток интегрируется с более широким рабочим процессом разработки (SDD/DDD/TDD/VDD) и обеспечивает:

- **Трассируемость решений**: Каждое архитектурное решение задокументировано с rationale
- **Сохранение контекста**: Будущие разработчики понимают почему были приняты решения
- **Процесс review**: Поэтапное утверждение обеспечивает валидацию решений
- **Cross-references**: Решения ссылаются на связанные ADR, requirements и implementations

## Фазы потока

```
DRAFT → REVIEW → APPROVED | REJECTED
  ↑        ↑         ↓
  └────────┴─────────┘ (итерация до решения)
```

### Фаза 1: DRAFT

**Input**: Архитектурный вопрос или решение требуется
**Output**: `flows/adr-[number]-[name]/adr.md`

Draft фиксирует начальное предложение решения:
- Контекст проблемы и drivers
- Рассмотренные варианты с pros/cons
- Предложенное решение
- Ожидаемые последствия

### Фаза 2: REVIEW

**Input**: Завершённый draft
**Output**: Обновлённый ADR с feedback от review

Review валидирует решение:
- Верификация технической точности
- Оценка влияния
- Рассмотрение альтернатив
- Сбор feedback от stakeholders

### Фаза 3: DECISION

**Статус**: APPROVED | REJECTED | SUPERSEDED

Финальное состояние решения:
- **APPROVED**: Решение принято и должно быть выполнено
- **REJECTED**: Решение не принято (задокументировать почему)
- **SUPERSEDED**: Заменено новым ADR (ссылка на замену)

---

## Структура директорий

```
flows/
├── adr.md                          # Этот файл (справка по потоку)
├── .templates/adr/                 # Шаблоны для новых ADR
│   ├── adr.md                      # Основной шаблон ADR
│   ├── _status.md                  # Шаблон отслеживания статуса
│   └── lightweight.md              # Lightweight ADR для малых решений
├── adr-index.md                    # Мастер-индекс всех ADR
└── adr-[NNN]-[decision-name]/      # Директория на ADR
    ├── adr.md                      # Основная запись решения
    ├── _status.md                  # Текущая фаза + статус review
    └── [attachments]/              # Поддерживающие диаграммы, исследования, и т.д.
```

---

## Запуск нового ADR

### Новое решение
```
/adr start [name]
```

Создаёт `flows/adr-[NNN]-[name]/` с шаблонами и открывает фазу DRAFT.
Автоматически назначает следующий доступный номер ADR.

### Возобновить существующий
```
/adr resume [number-or-name]
```

Читает `_status.md` для определения текущей фазы и продолжает оттуда.

### Quick/Lightweight
```
/adr quick [name]
```

Создаёт lightweight ADR для меньших решений используя упрощённый шаблон.

### Список всех
```
/adr list
```

Показывает все ADR с их статусом (draft/review/approved/rejected/superseded).

### Статус
```
/adr status
```

Показывает ADR текущие в фазе DRAFT или REVIEW.

---

## Переходы фаз

### DRAFT → REVIEW
- [ ] Все секции завершены
- [ ] Как минимум 2 варианта рассмотрено
- [ ] Последствия задокументированы
- [ ] Пользователь явно подтверждает: "ready for review"

### REVIEW → APPROVED
- [ ] Technical review завершён
- [ ] Stakeholder feedback address
- [ ] Нет блокирующих вопросов
- [ ] Reviewer явно подтверждает: "ADR approved"

### REVIEW → REJECTED
- [ ] Решение отклонено с задокументированным rationale
- [ ] Альтернативное направление замечено (если применимо)
- [ ] Reviewer явно указывает: "ADR rejected"

---

## Отслеживание статуса

Каждая директория ADR имеет `_status.md`:

```markdown
# Status: ADR-[NNN] [name]

## Current Phase
DRAFT | REVIEW | APPROVED | REJECTED | SUPERSEDED

## Phase Status
DRAFTING | AWAITING_REVIEW | IN_REVIEW | DECIDED

## Last Updated
[DATE] by [AGENT/PERSON]

## Reviewers
- [ ] [Reviewer 1]: [pending/approved/concerns]
- [ ] [Reviewer 2]: [pending/approved/concerns]

## Review Comments
- [Date]: [Comment from reviewer]

## Progress
- [ ] Draft создан
- [ ] Варианты задокументированы
- [ ] Последствия проанализированы
- [ ] Ready for review
- [ ] Review завершён
- [ ] Решение принято

## Supersedes
[Если этот ADR заменяет другой: ADR-XXX]

## Superseded By
[Если этот ADR был заменён: ADR-YYY]

## Related ADRs
- ADR-XXX: [описание взаимосвязи]
- ADR-YYY: [описание взаимосвязи]

## Implementation References
- [Ссылка на implementing PRs, issues, или specs]
```

---

## ADR Index (adr-index.md)

Индекс предоставляет поиск и cross-referencing в формате markdown таблицы:

```markdown
| # | Имя | Заголовок | Статус | Создан | Решён | Файл |
|---|------|-------|--------|---------|---------|------|
| 1 | use-tower-middleware | Use Tower for Middleware | approved | 2025-01-15 | 2025-01-20 | adr-001-use-tower-middleware/adr.md |
| 2 | redis-client-design | Redis Client Design | approved | 2025-01-18 | 2025-01-25 | adr-002-redis-client-design/adr.md |
```

Категории и взаимосвязи отслеживаются в отдельных секциях файла индекса.

---

## Интеграция с потоками разработки

### ADRs и SDD/DDD/TDD/VDD

ADRs дополняют потоки разработки:

1. **Во время Requirements**: Ссылаться на релевантные ADR которые constrain дизайн
2. **Во время Specifications**: Создать новые ADR для значимых решений
3. **Во время Implementation**: Ссылаться на ADR которые направляли implementation
4. **Post-Implementation**: Обновить ADR с lessons learned

### Cross-Referencing

В spec документах:
```markdown
## Архитектурные решения
Эта функция реализует решения из:
- ADR-001: Tower middleware архитектура
- ADR-002: Redis client дизайн
```

В ADRs:
```markdown
## Implementation References
- `flows/sdd-feature-x/02-specifications.md`
- PR #123: Initial implementation
```

---

## Структура шаблона ADR

### Стандартный шаблон ADR

```markdown
# ADR-[NNN]: [Заголовок]

## Meta
- **Number**: ADR-[NNN]
- **Type**: constraining | enabling (опционально)
- **Status**: DRAFT | REVIEW | APPROVED | REJECTED | SUPERSEDED
- **Created**: [date]
- **Decided**: [date, если решено]
- **Author**: [name]
- **Reviewers**: [names]

### Типы ADR
- **constraining**: Выбирает из вариантов, закрывает альтернативы (e.g., "Use PostgreSQL, not MongoDB")
- **enabling**: Добавляет новые возможности, расширяет scope (e.g., "Add Loongson support")

## Context

В чём проблема которую мы наблюдаем и что мотивирует это решение или изменение?

## Decision Drivers

- [driver 1: description]
- [driver 2: description]
- [driver 3: description]

## Рассмотренные варианты

### Вариант 1: [name]

**Описание**: [что влечёт этот вариант]

**Pros**:
- [advantage 1]
- [advantage 2]

**Cons**:
- [disadvantage 1]
- [disadvantage 2]

### Вариант 2: [name]

[та же структура]

### Вариант 3: [name]

[та же структура]

## Decision

Мы будем использовать **[выбранный вариант]** потому что [rationale].

## Consequences

### Positive
- [positive consequence 1]
- [positive consequence 2]

### Negative
- [negative consequence 1]
- [negative consequence 2]

### Neutral
- [neutral observation]

## Related Decisions
- ADR-XXX: [как связано]

## References
- [внешние ссылки, исследования, обсуждения]

## Tags
[space-separated tags для категоризации]
```

---

## Лучшие практики

### Делать
- Документировать решения как можно ближе к моменту решения
- Включать отклонённые варианты (они предоставляют контекст)
- Ссылаться на связанные ADR и specs
- Своевременно обновлять статус когда решения приняты
- Держать язык ясным и доступным

### Не делать
- Пропускать секцию "considered options"
- Оставлять ADR в DRAFT бесконечно
- Документировать детали implementation (это для specs)
- Забывать обновлять связанные ADR когда решения изменяются
- Использовать ADR для тривиальных решений (использовать inline комментарии вместо этого)

---

## Когда создавать ADR

Создавать ADR когда:
- Выбор между технологиями или фреймворками
- Проектирование архитектуры системы или основных компонентов
- Принятие решений влияющих на несколько частей системы
- Установление паттернов или конвенций
- Решения которые трудно обратить

Пропустить ADR когда:
- Рутинный выбор implementation
- Хорошо установленные паттерны уже задокументированы
- Решения с очевидным единственным выбором
- Временные или экспериментальные изменения

---

## Миграция с Legacy ADR

Если у вас есть существующие ADR в `.claude/` или других местах:

1. Создать `flows/adr-index.md` с метаданными существующих ADR
2. Переместить ADR в структуру `flows/adr-[NNN]-[name]/`
3. Добавить `_status.md` в каждую директорию ADR
4. Обновить cross-references в specs и коде

---

*Версия: 1.0*
*Создано: 2025-03-01*
