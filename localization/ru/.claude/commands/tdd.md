# Поток Tests-Driven Development

Вы входите в режим TDD (Tests-Driven Development). Прочитайте `flows/tdd.md` для полной справки по потоку.

## Команда: $ARGUMENTS

Распарсите аргументы для определения действия:

### `start [name]` — Запустить новый поток TDD
1. Создать директорию `flows/tdd-[name]/`
2. Скопировать шаблоны из `flows/.templates/tdd/`
3. Создать `_status.md` с phase = REQUIREMENTS
4. Начать elicitation требований с пользователем

### `resume [name]` — Возобновить существующий поток
1. Прочитать `flows/tdd-[name]/_status.md` для определения текущей фазы
2. Прочитать все существующие артефакты в директории документов
3. Сообщить пользователю текущее состояние
4. Продолжить с места остановки

### `fork [existing] [new]` — Fork для восстановления контекста
1. Скопировать `flows/tdd-[existing]/` в `flows/tdd-[new]/`
2. Обновить `_status.md` с заметкой о происхождении fork
3. Спросить пользователя какие adjustments сделать
4. Продолжить с текущей фазы с модификациями

### `status` — Показать все активные потоки TDD
1. Список всех директорий `flows/tdd-*/`
2. Прочитать каждый `_status.md` и резюмировать фазу + blockers

### Нет аргументов или `help`
1. Показать доступные команды и текущие активные потоки

---

## Поведения фаз

### Фаза REQUIREMENTS
- Получить от пользователя что он хочет построить и зачем
- Задать уточняющие вопросы
- Задокументировать user stories с acceptance criteria
- Идентифицировать constraints и non-goals
- Итеративно обновлять `01-requirements.md`
- Ждать явного "requirements approved" перед переходом

### Фаза TESTS (TDD-specific) — Cases-First мышление

**Критично**: Это НЕ о написании test кода. Это об исчерпывающем поведенческом анализе.

**Cases-First подход:**
```
1. ENUMERATE ALL BEHAVIORS
   - Happy paths, edge cases, error cases
   - State transitions, race conditions
   - ВСЕ сценарии определяющие корректность

2. DEFINE SUCCESS CRITERIA FOR EACH
   - Точные ожидаемые outcomes
   - State changes
   - Outputs произведённые

3. DERIVE DESIGN FROM CASES
   - Cases выявляют необходимые interfaces
   - Cases выявляют data structures
   - Cases выявляют error handling needs
```

**На документ case:**
- Given: Полное начальное состояние
- When: Точное действие/event
- Then: Точный ожидаемый outcome
- Design implications: Какой interface/structure требует этот case

**Проверка полноты перед подтверждением:**
- [ ] Все requirements имеют behaviors
- [ ] Все edge cases идентифицированы
- [ ] Все error scenarios определены
- [ ] Design implications извлечены

- Итеративно обновлять `02-tests.md`
- Ждать явного "tests approved" перед переходом

### Фаза SPECIFICATIONS — Выведено из Tests

**Критично**: Specs ВЫВЕДЕНЫ из test cases, не изобретены независимо.

**Derivation:**
```
Test Cases → Implied Interfaces → Specs
Test Cases → Implied Data Structures → Specs
Test Cases → Implied Error Handling → Specs
```

**Каждый элемент spec должен трассироваться к tests:**
- Interface существует потому что tests требуют его
- Data structure существует потому что tests оперируют на ней
- Error type существует потому что tests ожидают его

- Анализировать codebase на затронутые системы
- Спроектировать interfaces и data models (выведено из tests)
- Задокументировать edge cases (из test cases)
- Включить traceability matrix (элемент spec → tests)
- Итеративно обновлять `03-specifications.md`
- Ждать явного "specs approved" перед переходом

### Фаза PLAN
- Разбить specs на атомарные задачи
- Идентифицировать изменения файлов и dependencies
- Оценить сложность
- Итеративно обновлять `04-plan.md`
- Ждать явного "plan approved" перед переходом

### Фаза IMPLEMENTATION
- Выполнять план задача за задачей
- Следовать протоколу тестирования CLAUDE.md (один test за раз)
- Логировать прогресс в `05-implementation-log.md`
- Документировать любые отклонения от плана
- Убедиться что все определённые tests проходят
- Ждать завершения implementation перед переходом

### Фаза DOCUMENTATION
- Создать client-facing README.md
- Объяснить функцию простыми, нетехническими терминами
- Предоставить объяснение "how it works" используя аналогии
- Добавить практические usage примеры
- Фокус на преимуществах и функциональности с client перспективы
- Избегать технического жаргона
- Ждать явного "docs approved" перед завершением

---

## Всегда

- Обновлять `_status.md` после каждого значимого изменения
- Никогда не пропускать фазы и не предполагать подтверждение
- При неуверенности спрашивать, а не предполагать
- Перед завершением сессии убедиться, что заметки для передачи полны
- Помнить: фаза Tests это CASES-FIRST — исчерпывающий поведенческий анализ
- Дизайн ВОЗНИКАЕТ из cases, не наоборот
- Каждый элемент spec должен трассироваться к test cases
