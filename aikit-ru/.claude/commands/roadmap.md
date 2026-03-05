# Roadmap — DFS оркестрация потоков

Depth-first разработка: кратчайший путь к working функциональности.

## Первый запуск: Инициализация из шаблонов

**Перед любым выполнением**, проверить существует ли `flows/roadmap/`:

```
IF flows/roadmap/ не существует:
  1. Скопировать flows/.templates/roadmap/ → flows/roadmap/
  2. Сообщить пользователю: "Initialized roadmap workspace from templates"
  3. Продолжить выполнение
```

## Команда: $ARGUMENTS

```
/roadmap                      # DFS к MVP (minimum viable product)
/roadmap [goal]               # DFS к конкретной цели
/roadmap status               # Показать текущее состояние без выполнения
```

### Аргументы

| Аргументы | Режим | Поведение |
|-----------|------|----------|
| нет | DFS к MVP | Кратчайший путь к working core функциональности |
| `[goal]` | DFS к цели | Кратчайший путь к достижению указанной цели |
| `status` | Только просмотр | Показать dependency graph и прогресс |

**Примеры:**
```
/roadmap                          # MVP: auth + core API working
/roadmap "user can login"         # Цель: всё нужное для login
/roadmap "dashboard shows data"   # Цель: всё для dashboard
```

---

## Ключевой принцип: DFS (Depth-First)

**Реализовать МИНИМУМ пути для достижения цели, завершая каждый элемент ПОЛНОСТЬЮ перед переходом к следующему.**

```
Цель: "user can login"

DFS путь найден:
  sdd-database ──blocks──> sdd-auth ──blocks──> [GOAL]

Порядок выполнения (depth-first, завершить каждый):
  1. sdd-database: REQ → SPEC → PLAN → IMPLEMENT ✓
  2. sdd-auth: REQ → SPEC → PLAN → IMPLEMENT ✓
  3. Goal achieved!

NOT touched (не на критическом пути):
  - ddd-dashboard
  - sdd-reporting
  - tdd-analytics
```

---

## DFS execution flow

```
┌─────────────────────────────────────────────────────────┐
│                    DFS ROADMAP                          │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  Цель: "user can login"                                 │
│                                                         │
│  Анализ зависимостей:                                   │
│    sdd-database ──> sdd-auth ──> [GOAL]                │
│                                                         │
│  ┌─────────────────────────────────────────────────┐   │
│  │              sdd-database (blocker)              │   │
│  │  ┌─────┐  ┌──────┐  ┌──────┐  ┌───────────┐   │   │
│  │  │ REQ │→ │ SPEC │→ │ PLAN │→ │ IMPLEMENT │   │   │
│  │  └──┬──┘  └──┬───┘  └──┬───┘  └─────┬─────┘   │   │
│  │     ▼        ▼         ▼            ▼         │   │
│  │  approve  approve   approve      COMPLETE     │   │
│  └─────────────────────────────────────────────────┘   │
│                          │                             │
│                          ▼ unblocked                   │
│  ┌─────────────────────────────────────────────────┐   │
│  │              sdd-auth (target)                   │   │
│  │  ┌─────┐  ┌──────┐  ┌──────┐  ┌───────────┐   │   │
│  │  │ REQ │→ │ SPEC │→ │ PLAN │→ │ IMPLEMENT │   │   │
│  │  └──┬──┘  └──┬───┘  └──┬───┘  └─────┬─────┘   │   │
│  │     ▼        ▼         ▼            ▼         │   │
│  │  approve  approve   approve      COMPLETE     │   │
│  └─────────────────────────────────────────────────┘   │
│                          │                             │
│                          ▼                             │
│                    GOAL ACHIEVED                       │
│                                                         │
└─────────────────────────────────────────────────────────┘
```

---

## MVP режим (нет аргументов)

Когда цель не указана, найти MVP = минимум потоков для функционирования core.

```
MVP Detection:
1. Найти потоки без dependents (leaf nodes)
2. Найти потоки с наибольшим количеством dependents (core nodes)
3. MVP = core nodes которые включают basic функциональность
4. DFS к завершению MVP пути
```

---

## Шаги выполнения

### Шаг 1: Анализ зависимостей

```
1. Прочитать flows/roadmap/_status.md
2. Сканировать все flows/sdd-*/, flows/ddd-*/, flows/tdd-*/, flows/vdd-*/
3. Прочитать каждый _status.md и документы
4. Прочитать flows/adr-*/ для контекста
5. Построить dependency graph
6. Обновить flows/roadmap/dependencies.md
```

### Шаг 2: Определение цели

```
IF цель указана:
  1. Распарсить цель в target flow(s)
  2. Найти все blockers рекурсивно
  3. Построить критический путь

IF нет цели (MVP режим):
  1. Идентифицировать core flows (наибольшее количество зависимостей)
  2. Построить минимальный working путь
  3. Представить MVP scope для подтверждения
```

### Шаг 3: Показать DFS путь

Всегда отображать перед продолжением:

```
=== ROADMAP DFS STATUS ===

Цель: "user can login"

Критический путь (DFS порядок):
  1. sdd-database    [COMPLETE] ✓
  2. sdd-auth        [IN PROGRESS] ← current
     - REQ: approved ✓
     - SPEC: approved ✓
     - PLAN: drafting...
     - IMPL: pending

Not on path (пропущено):
  ○ ddd-dashboard
  ○ sdd-reporting

ADRs (контекст):
  ◆ ADR-001 [approved] Use PostgreSQL

Next: Complete plan for sdd-auth

=============================
```

### Шаг 4: Выполнение DFS

**Для каждого потока на критическом пути (в порядке зависимостей):**

```
COMPLETE_FLOW(flow):
  1. Requirements:
     - Draft если нужно
     - Спросить: "requirements approved?"
     - SYNC статус к roadmap И потоку

  2. Specifications:
     - Draft если нужно
     - Спросить: "specs approved?"
     - SYNC статус к roadmap И потоку

  3. Plan:
     - Draft если нужно
     - Спросить: "plan approved?"
     - SYNC статус к roadmap И потоку

  4. Implementation:
     - Выполнять задачи плана
     - Обновить implementation-log
     - SYNC статус к roadmap И потоку

  5. Пометить COMPLETE, перейти к следующему на пути
```

---

## Синхронизация статуса

**КРИТИЧНО**: Каждое изменение статуса обновляет ДВА места:

```
┌────────────────────┐        ┌────────────────────┐
│ flows/roadmap/     │  sync  │ flows/[type]-[x]/  │
│   _status.md       │◄──────►│   _status.md       │
└────────────────────┘        └────────────────────┘

Пример: Когда sdd-auth plan approved:

1. Обновить flows/sdd-auth/_status.md:
   - Current Phase: IMPLEMENTATION
   - Phase Status: APPROVED
   - Progress: [x] Plan approved

2. Обновить flows/roadmap/_status.md:
   - Current Flow: sdd-auth
   - sdd-auth: PLAN_APPROVED → IMPLEMENTING
   - Path Progress: 1/2 complete
```

### Протокол Sync

```
SYNC(flow-name, artifact, status):
  1. Обновить flows/[type]-[flow-name]/_status.md
     - Установить статус artifact
     - Обновить progress checklist
     - Установить current phase если продвижение

  2. Обновить flows/roadmap/_status.md
     - Обновить поток в критическом пути
     - Обновить path progress
     - Установить current flow если изменён

  3. Обновить flows/roadmap/log.md
     - Log: "[timestamp] [flow-name] [artifact] → [status]"
```

---

## Структура директорий

```
flows/roadmap/
├── _status.md              # DFS состояние + critical path tracker
├── dependencies.md         # Dependency graph (полный, не только путь)
├── plan.md                 # Current path execution plan
└── log.md                  # Все изменения статуса и действия
```

---

## Структура _status.md

```markdown
# Roadmap Status

## Режим: DFS

## Цель

"user can login" | MVP (auto-detected)

## Критический путь

| Order | Flow | Status | Phase | Progress |
|-------|------|--------|-------|----------|
| 1 | sdd-database | COMPLETE | - | 100% |
| 2 | sdd-auth | IN_PROGRESS | PLAN | 60% |

## Current Focus

- **Flow**: sdd-auth
- **Phase**: PLAN
- **Status**: DRAFTING
- **Blockers**: none

## Path Progress

- Потоков завершено: 1/2
- Прогресс текущего потока: 60%
- Общий: 80%

## Пропущенные потоки (не на пути)

- ddd-dashboard (зависит от sdd-auth, не нужно для цели)
- sdd-reporting (независимый, не в цели)

## Last Action

[timestamp] Approved specifications for sdd-auth

## Next Action

1. Complete plan for sdd-auth
2. Get plan approval
3. Begin implementation
```

---

## Сравнение с BFS (/waterfall)

| Аспект | /roadmap (DFS) | /waterfall (BFS) |
|--------|----------------|------------------|
| Цель | Достичь конкретной цели | Завершить всё |
| Порядок | Один поток полностью: REQ→SPEC→PLAN→IMPL | Все REQ → Все SPEC → Все PLAN → Implement |
| Когда | MVP или конкретная функция | Полное планирование проекта |
| Результат | Цель достигнута, другие не тронуты | Все потоки полностью задокументированы |
| Скорость | Быстрее к первому working коду | Медленнее, но комплексно |

---

## Обработка ADR

ADRs **read-only** в roadmap:
- Читать для контекста (constraining/enabling решения)
- НЕ создавать новые ADRs
- Ссылаться в анализе зависимостей
- Заметить если ADR влияет на критический путь

---

## Переходы фаз

Те же правила что для индивидуальных потоков:
- "requirements approved" → SPEC фаза
- "specs approved" → PLAN фаза
- "plan approved" → IMPLEMENTATION фаза

---

## Всегда

- Показывать DFS путь перед выполнением
- Завершать каждый поток ПОЛНОСТЬЮ перед переходом к следующему
- SYNC статус к ОБОИМ roadmap и индивидуальному потоку
- Пропускать потоки не на критическом пути
- Спрашивать подтверждение на переходах фаз
- Логировать всё в log.md
- Никогда не пропускать фазы внутри потока
