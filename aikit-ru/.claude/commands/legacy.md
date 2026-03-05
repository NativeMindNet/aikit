# Legacy — Reverse Engineering документации

Анализирует существующий код и автоматически генерирует документы (потоки ADR/SDD/DDD/TDD/VDD).

## Шаг 0: Сканирование существующих потоков (ПЕРЕД ВСЕМ ОСТАЛЬНЫМ)

**КРИТИЧНО**: Перед любым анализом просканировать и понять существующие потоки.

```
1. СКАНИРОВАТЬ все существующие потоки:
   - flows/adr-*/**/*.md (Architectural Decision Records)
   - flows/sdd-*/**/*.md (Spec-Driven Development)
   - flows/ddd-*/**/*.md (Document-Driven Development)
   - flows/tdd-*/**/*.md (Tests-Driven Development)
   - flows/vdd-*/**/*.md (Visual-Driven Development)

2. ПОСТРОИТЬ индекс потоков:
   | Путь потока | Тип | Темы покрыты | Ключевые решения |
   |-----------|------|----------------|---------------|
   | flows/sdd-auth/ | SDD | JWT, sessions | Bcrypt hashing |
   | flows/adr-001-jwt/ | ADR | Token format | RS256 chosen |
   | ... | ... | ... | ... |

3. СОХРАНИТЬ в _traverse.md в секции ## Existing Flows

4. ИСПОЛЬЗОВАТЬ этот индекс для matching во время анализа
```

**Назначение**: Знать какая документация уже существует перед созданием или обновлением.

---

## Первый запуск: Инициализация из шаблонов

**После сканирования существующих потоков**, проверить существует ли `flows/legacy/`:

```
IF flows/legacy/ не существует:
  1. Скопировать flows/.templates/legacy/ → flows/legacy/
  2. Сообщить пользователю: "Initialized legacy workspace from templates"
  3. Продолжить выполнение
```

## Команда: $ARGUMENTS

```
/legacy                              # BFS из корня проекта
/legacy src/auth                     # BFS из src/auth (анализировать только это поддерево)
/legacy src/auth "JWT validation"    # DFS: фокус на "JWT validation" внутри src/auth
```

### Выбор режима

| Аргументы | Режим | Поведение |
|-----------|------|----------|
| нет | BFS | Полный анализ проекта, breadth-first |
| только path | BFS | Анализировать поддерево начиная с path |
| path + "комментарий" | DFS | Глубокий фокус на теме комментария внутри path |

**Path** = ГДЕ искать исходный код (точка старта)
**Комментарий** = НА ЧЁМ фокусироваться (запускает DFS режим)

---

## Ключевая концепция: Дерево рекурсивного понимания

**Критично**: Это НЕ отражение файловой системы. AI строит **логическое дерево понимания**, которое растёт в ГЛУБИНУ, не в ширину.

```
НЕПРАВИЛЬНО (отражение файловой системы):
analysis/src/auth/jwt/tokens/_analysis.md  ❌

НЕПРАВИЛЬНО (плоская структура):
analysis/depth-0.md, depth-1.md, depth-2.md  ❌

ПРАВИЛЬНО (логическое дерево растущее в глубину):
understanding/
├── _root.md                      # Обзор проекта
└── core-domain/                  # Логическая концепция (НЕ путь к исходникам!)
    ├── _node.md                  # Понимание этого уровня
    └── authentication/           # Более глубокая концепция
        ├── _node.md
        └── token-lifecycle/      # Ещё глубже
            └── _node.md
```

Каждая директория = логическая концепция, обнаруженная во время анализа.
Каждый `_node.md` = понимание на этом уровне глубины.
Дерево растёт когда AI обнаруживает более глубокие концепции для исследования.

---

## Алгоритм рекурсивного обхода

AI выполняет **depth-first traversal** дерева понимания, сохраняя состояние в `_traverse.md`.

```
RECURSIVE-UNDERSTAND(node):
    1. ENTER   → Push в стек, создать _node.md, сформировать гипотезу
    2. EXPLORE → Прочитать исходный код, валидировать понимание
    3. SPAWN   → Идентифицировать дочерние концепции требующие более глубокого анализа
    4. RECURSE → Для каждого ребёнка: RECURSIVE-UNDERSTAND(child)
    5. SYNTH   → Комбинировать insights детей, обновить _node.md
    6. EXIT    → Pop из стека, bubble summary к родителю
```

### Диаграмма фаз

```
        ┌─────────────────────────────────────────┐
        │                                         │
        ▼                                         │
    ENTERING ──► EXPLORING ──► SPAWNING           │
        │                          │              │
        │                          ▼              │
        │                    [есть дети?]         │
        │                     /         \         │
        │                   yes          no       │
        │                    │            │       │
        │                    ▼            │       │
        │               RECURSE ──────────┤       │
        │              (для каждого)      │       │
        │                    │            │       │
        │                    ▼            │       │
        │               SYNTHESIZING ◄────┘       │
        │                    │                    │
        │                    ▼                    │
        │                 EXITING                 │
        │                    │                    │
        │                    ▼                    │
        │         [сгенерировать ADR/SDD/DDD/TDD/VDD]  │
        │                    │                    │
        │                    ▼                    │
        └─────────────── [записать состояние] ────┘
                            │
                            ▼
                     [pop из стека]
                     [bubble up к родителю]
```

---

## Персистентность состояния: _traverse.md

AI читает `_traverse.md` чтобы знать точно где он находится и что делать дальше.

### Структура стека

```markdown
## Existing Flows Index

| Путь потока | Тип | Темы | Ключевые решения |
|-----------|------|--------|---------------|
| flows/sdd-auth/ | SDD | authentication, JWT, sessions | bcrypt, RS256 |
| flows/adr-001-jwt/ | ADR | token format | RS256 chosen |
| flows/tdd-crypto/ | TDD | hashing, encryption | AES-256 |

## Current Stack

/ (root)                           DONE
└── core-domain                    DONE
    └── authentication             EXPLORING ← текущая позиция
        └── token-management       PENDING (ребёнок для исследования)
```

### Протокол возобновления

1. Прочитать `_traverse.md`
2. Найти вершину стека (текущую позицию)
3. Проверить фазу (ENTERING/EXPLORING/SPAWNING/SYNTHESIZING/EXITING)
4. Выполнить действия этой фазы
5. Обновить `_traverse.md` новым состоянием
6. Продолжить или приостановить для следующего вызова

### Действия фаз

| Фаза | Читать | Писать | Далее |
|------|--------|--------|-------|
| ENTERING | Пути к исходникам, existing_flows_index | _node.md (гипотеза) | EXPLORING |
| EXPLORING | Исходный код | _node.md (валидировано) | SPAWNING |
| SPAWNING | _node.md | Ожидающие дети | RECURSE или SYNTH |
| SYNTHESIZING | Саммари детей | _node.md (синтез) | EXITING |
| EXITING | _node.md, existing_flows_index | **1. Match flow** | Match/Update |
| Match/Update | Существующие потоки | **2. ADR/SDD/DDD/TDD/VDD документы** | Запись состояния |
| Record State | Все документы | **3. _traverse.md, log.md, index** | Pop стек |

---

## Структура директорий

```
flows/legacy/
├── _status.md                    # Общий прогресс
├── _traverse.md                  # Стек рекурсии & состояние
├── log.md                        # История итераций
├── understanding/                # Дерево (растёт в глубину)
│   ├── _root.md                  # Точка входа
│   ├── _node.template.md         # Шаблон для новых узлов
│   └── [domain]/                 # Первая логическая область
│       ├── _node.md
│       └── [subdomain]/          # Глубже...
│           ├── _node.md
│           └── [concept]/        # Ещё глубже...
│               └── _node.md
├── mapping.md                    # Node → Flow mapping
└── review.md                     # Элементы для human review
```

---

## Определение типа потока

Потоки на модуль, не на тип файла.

### Решение по назначению

| Назначение | Тип потока |
|------------|-----------|
| Логика внутреннего сервиса | SDD |
| Функция для stakeholder-facing | DDD |
| Логика критичная к корректности | TDD |
| User experience первичен | VDD |

### Индикаторы TDD (Cases-First)
- Высокое test coverage (>80%)
- Tests определяют поведение, не верифицируют implementation
- Edge cases имеют значение, failures имеют последствия

### Индикаторы DDD (Stakeholder Communication)
- Нужно объяснение клиентам/руководителям
- Функция «продаваемая»
- Документация является deliverable

---

## Шаги выполнения

### Шаг 1: Сканирование существующих потоков

```
1. Найти все существующие потоки в директории flows/:
   - Glob: flows/adr-*/**/*.md
   - Glob: flows/sdd-*/**/*.md
   - Glob: flows/ddd-*/**/*.md
   - Glob: flows/tdd-*/**/*.md
   - Glob: flows/vdd-*/**/*.md

2. Для каждого потока извлечь метаданные:
   - Тип потока (ADR|SDD|DDD|TDD|VDD)
   - Темы покрыты (из заголовков и секций документов)
   - Ключевые решения (из секций context/decision ADR)
   - Границы модулей (из specifications)

3. Построить индекс в памяти:
   ```
   existing_flows = [
     {
       path: "flows/sdd-auth/",
       type: "SDD",
       topics: ["authentication", "JWT", "sessions"],
       decisions: ["bcrypt for passwords", "JWT for tokens"]
     },
     ...
   ]
   ```

4. Сохранить в _traverse.md:
   ```markdown
   ## Existing Flows Index

   [таблица существующих потоков с темами]
   ```
```

### Шаг 2: Инициализация обхода

```
1. Прочитать _traverse.md
2. Если пуст: создать root node, push в стек
3. Определить режим из аргументов
4. Загрузить индекс существующих потоков из _traverse.md
```

### Шаг 3: Обход (рекурсивный)

Выполнить текущую фазу, обновить состояние, продолжить или приостановить.

**Каждый вызов:**
```
1. Прочитать _traverse.md (текущая позиция)
2. Выполнить действия фазы
3. Обновить _traverse.md (новое состояние)
4. Обновить _node.md (текущее понимание)
5. Если ещё работа: продолжить
6. Если приостановлено/прервано: состояние сохранено
```

### Шаг 4: Match Flow (перед генерацией)

**КРИТИЧНО**: Перед созданием любого потока, поиск существующего matching потока.

```
MATCH-FLOW(node_understanding, existing_flows_index):
    1. Извлечь keywords из понимания узла:
       - Имя модуля (e.g., "authentication")
       - Ключевые концепции (e.g., "JWT", "tokens", "sessions")
       - Технологии (e.g., "bcrypt", "RS256")

    2. Для каждого existing_flow в existing_flows_index:
       - Вычислить overlap_score = count(keywords в existing_flow.topics)
       - Вычислить decision_match = count(keywords в existing_flow.decisions)
       - total_score = overlap_score + decision_match

    3. Выбрать best_match = поток с наивысшим score

    4. IF best_match.score >= 2:
       - RETURN best_match (сильный match найден)
       ELSE:
       - RETURN null (нет подходящего match, создать новый)
```

**Пример:**
```
Понимание узла: "authentication with JWT tokens, bcrypt passwords"
Keywords: ["authentication", "JWT", "tokens", "bcrypt", "passwords"]

Matching против:
- flows/sdd-auth/: topics=["authentication", "JWT", "sessions"] → score=3 ✓
- flows/sdd-api/: topics=["REST", "endpoints"] → score=0 ✗
- flows/tdd-crypto/: topics=["hashing", "bcrypt"] → score=2 ✓

Лучший match: flows/sdd-auth/ (score=3)
Действие: APPEND к flows/sdd-auth/
```

### Шаг 5: Генерация/Обновление потоков (перед записью итерации)

**КРИТИЧНО**: Перед записью статуса итерации в _traverse.md, обновить или создать документы потоков.

```
IF matching_flow существует:
  1. Прочитать существующие 01-requirements.md, 02-specifications.md
  2. Сравнить анализ с существующим контентом
  3. IF найдены новые insights:
     - APPEND к релевантным секциям:
       ```markdown
       ## [Имя секции] — Legacy Additions
       > Добавлено /legacy на [DATE]

       - [новый insight обнаружен]
       - [нюанс не задокументированный ранее]
       ```
     - НЕ модифицировать существующий контент
  4. IF обнаружен конфликт (анализ противоречит существующему):
     - STOP и ASK пользователя немедленно
     - "Found conflict in [flow]: [описание]. Which is correct?"
  5. Логировать обновление в log.md

ELSE (нет matching потока):
  1. Создать новый flows/[type]-[name]/
  2. Сгенерировать 01-requirements.md из понимания
  3. Сгенерировать 02-specifications.md из анализа кода
  4. Status = DRAFT
  5. Добавить в existing_flows_index в _traverse.md
```

### Шаг 6: Генерация/Обновление ADR (перед записью итерации)

**КРИТИЧНО**: Перед записью статуса итерации в _traverse.md, обновить или создать документы ADR.

```
Для каждого обнаруженного архитектурного решения:
  1. Извлечь keywords решения (e.g., "RS256", "token format", "JWT")
  2. Искать existing_flows_index для ADR с matching темами
  3. IF match найден (score >= 2):
     - Прочитать существующий документ ADR
     - APPEND новый контекст/insights:
       ```markdown
       ## Additional Context — Legacy Analysis
       > Добавлено /legacy на [DATE]

       - [additional context обнаружен]
       ```
  4. IF нет match:
     - Создать flows/adr-[NNN]-[name]/
     - Type: constraining | enabling
     - Status = DRAFT
     - Добавить в existing_flows_index
```

### Шаг 7: Запись статуса итерации (после всех документов)

**ТОЛЬКО ПОСЛЕ** обновления или создания документов ADR/SDD/DDD/TDD/VDD:
```
1. Обновить _traverse.md новым состоянием
2. Обновить _node.md текущим пониманием
3. Логировать итерацию в log.md
4. Обновить existing_flows_index если созданы новые потоки
5. Продолжить или приостановить для следующего вызова
```

**Порядок КРИТИЧЕН**: Документы ПЕРВЫМИ, затем персистентность состояния.

---

## Идемпотентность & Существующие потоки

Команда безопасна для многократного запуска. Автоматически matching и обновляет существующие потоки.

### Алгоритм matching потоков

```
Для каждого обнаруженного модуля/решения во время фазы EXITING:

1. ИЗВЛЕЧЬ keywords из понимания узла:
   - Имена модулей/doменов
   - Ключевые концепции и технологии
   - Архитектурные решения

2. ИСКАТЬ existing_flows_index:
   - Сравнить keywords против тем существующих потоков
   - Вычислить score matching (count overlap)

3. РЕШИТЬ:
   - IF score >= 2: UPDATE существующего потока (append-only)
   - IF score < 2: CREATE новый поток
```

### Протокол обновления существующего потока

```
IF matching поток найден:
  1. ПРОЧИТАТЬ существующие документы (01-requirements.md, 02-specifications.md)

  2. СРАВНИТЬ анализ с существующим контентом:
     - Идентифицировать gaps (вещи не задокументированные)
     - Идентифицировать conflicts (противоречия)
     - Идентифицировать confirmations (уже задокументировано корректно)

  3. ОБРАБОТАТЬ КАЖДОЕ:
     - Gaps → APPEND новую секцию:
       ```markdown
       ## [Секция] — Legacy Additions
       > Добавлено /legacy на [DATE]

       - [новый insight из анализа кода]
       ```

     - Conflicts → STOP и ASK:
       "Found conflict in [flow]: analysis показывает X, doc говорит Y. Which is correct?"

     - Confirmations → Действий не нужно

  4. ЛОГИРОВАТЬ обновление в log.md:
     - "Updated flows/sdd-auth/: appended 3 new insights"
```

### Протокол создания нового потока

```
IF нет matching потока (score < 2):
  1. ОПРЕДЕЛИТЬ тип потока (SDD|DDD|TDD|VDD|ADR)
  2. СОЗДАТЬ flows/[type]-[name]/ директорию
  3. СГЕНЕРИРОВАТЬ документы из понимания:
     - 01-requirements.md
     - 02-specifications.md (для SDD/DDD/TDD/VDD)
     - context/decision/consequences (для ADR)
  4. УСТАНОВИТЬ status = DRAFT
  5. ДОБАВИТЬ в existing_flows_index в _traverse.md
  6. ЛОГИРОВАТЬ создание в log.md:
     - "Created flows/sdd-auth/: authentication module"
```

### Спрашивать немедленно, не откладывать

**Нет review.md** — задавать вопросы по мере возникновения:

```
НЕПРАВИЛЬНО:
  - Найти конфликт → записать в review.md → продолжить → пользователь review позже ❌

ПРАВИЛЬНО:
  - Найти конфликт → STOP → спросить пользователя → получить ответ → продолжить ✓
  - Неясно о направлении → ASK перед углублением ✓
  - Несколько интерпретаций → ASK какая одна ✓
```

### Когда спрашивать

- Существующий поток противоречит анализу
- Возможны несколько границ модуля
- Неясен тип потока (SDD vs DDD vs TDD)
- Нельзя определить deprecated код или active
- Архитектурное решение неясно
- Score matching borderline (ровно 2, неоднозначно)

### Additive-only изменения

При обновлении существующих потоков:
```markdown
## [Существующая секция]
[оригинальный контент без изменений]

## [Существующая секция] — Legacy Additions
> Добавлено /legacy на [DATE]

- [новый insight обнаружен]
- [нюанс не задокументированный ранее]
```

**НИКОГДА:**
- Не удалять существующий контент
- Не модифицировать существующие предложения
- Не переупорядочивать существующие секции

**ВСЕГДА:**
- Append новые секции с подзаголовком "Legacy Additions"
- Префикс с датой и источником
- Держать оригинальный контент нетронутым

### Восстановление состояния
- _traverse.md полностью описывает текущую позицию
- Можно возобновить с любого прерывания
- Действия фаз идемпотентны

---

## Пример обхода

```
Вызов 1:
  Чтение _traverse.md: пусто
  Действие: Создать root, ENTERING
  Запись: understanding/_root.md
  Обновление: _traverse.md (стек: [/ ENTERING])

Вызов 2:
  Чтение: стек = [/ ENTERING]
  Действие: EXPLORING root
  Запись: _root.md (валидированное понимание)
  Обновление: _traverse.md (стек: [/ EXPLORING])

Вызов 3:
  Чтение: стек = [/ EXPLORING]
  Действие: SPAWNING — найден домен "authentication"
  Запись: Ожидающие дети: [authentication]
  Создание: understanding/authentication/
  Обновление: _traverse.md (стек: [/ SPAWNING], pending: [auth])

Вызов 4:
  Чтение: стек = [/ SPAWNING], pending: [auth]
  Действие: RECURSE в authentication
  Push: authentication в стек
  Обновление: _traverse.md (стек: [/ SPAWNING, auth ENTERING])

Вызов 5:
  Чтение: стек = [/ SPAWNING, auth ENTERING]
  Действие: ENTERING authentication
  Запись: understanding/authentication/_node.md
  ... продолжается глубже ...
```

---

## Структура _node.md

```markdown
# Understanding: [Логическое имя]

## Фаза: EXPLORING

## Гипотеза
[Начальное предположение]

## Источники
- src/auth/*.ts — authentication логика
- src/middleware/auth.ts — middleware

## Валидированное понимание
[Подтверждено после анализа кода]

## Дети
| Ребёнок | Статус |
|-------|--------|
| token-lifecycle | PENDING |
| session-mgmt | PENDING |

## Рекомендация потока
Тип: SDD
Уверенность: high
Rationale: Внутренний сервис, не нужны stakeholder docs

## Bubble Up
- Обрабатывает JWT validation
- Зависит от crypto модуля
```

---

## Всегда

- **ПЕРВЫМ**: Сканировать все существующие потоки перед любым анализом
- **MATCH**: Искать существующие matching потоки перед созданием новых
- **APPEND**: Обновлять существующие потоки только секциями "Legacy Additions"
- **ASK**: Stop немедленно на конфликтах, не откладывать
- **CREATE**: Новые потоки только когда score < 2
- **INDEX**: Обновлять existing_flows_index при создании новых потоков
- **PERSIST**: Состояние после КАЖДОГО действия
- **ДЕРЕВО**: Растёт в ГЛУБИНУ (концепции внутри концепций)
- **ИМЕНА**: ЛОГИЧЕСКИЕ (не пути к исходникам)
- **ОДИН**: Node может ссылаться на МНОГО исходных файлов
- **RESUME**: Из _traverse.md
- **DRAFT**: Потоки создаются только в статусе DRAFT
- **НИКОГДА**: Auto-approve или overwrite существующего контента
