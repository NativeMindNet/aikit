# ADR Guide — Справочный материал

Справочное руководство по Architecture Decision Records. Используйте `/adr` для создания и управления ADR.

## Адаптации для конкретных языков

### Rust Проекты

**Структура директорий**:
- Размещайте ADR в корне workspace (для multi-crate проектов)
- Рассмотрите отдельные ADR для решений основных crate
- Документируйте решения по feature flag в `Cargo.toml`
- Отслеживайте обоснования обновления зависимостей

**Категории распространённых ADR**:
- `performance` — Оптимизация, результаты benchmarking
- `safety` — Memory safety, паттерны concurrency
- `api-design` — Эволюция public API, breaking changes
- `dependencies` — Выбор crate, version pinning
- `testing` — Property testing, стратегии fuzzing

### Python Проекты

**Структура директорий**:
- Совместимо со структурами `pyproject.toml` и `setup.py`
- Рассмотрите ADR для стратегий virtual environment
- Документируйте решения по packaging и дистрибуции

**Категории распространённых ADR**:
- `packaging` — Дистрибуция, управление зависимостями
- `typing` — Type hints, конфигурация mypy
- `async` — Паттерны asyncio, подходы к concurrency
- `testing` — Стратегии pytest, организация tests

### JavaScript/TypeScript Проекты

**Структура директорий**:
- Работает с monorepos (nx, lerna, rush)
- Рассмотрите ADR для решений по build tool
- Документируйте пути миграции фреймворков

**Категории распространённых ADR**:
- `bundling` — Решения Webpack, vite, rollup
- `typing` — TypeScript, конфигурация
- `state-management` — Паттерны Redux, zustand, context
- `testing` — Стратегии Jest, vitest, cypress

### Go Проекты

**Структура директорий**:
- Выравнивание по структуре Go module
- Рассмотрите ADR для организации package
- Документируйте решения по дизайну interface

**Категории распространённых ADR**:
- `concurrency` — Паттерны Goroutine, использование channel
- `error-handling` — Error wrapping, custom errors
- `interfaces` — Дизайн API, уровни абстракции
- `dependencies` — Выбор module, стратегии vendor

---

## Соображения масштабирования

### Малые проекты (1-10 ADR)
- Простая структура директорий
- Базовое cross-referencing
- Ручное поддержание index допустимо

### Средние проекты (10-50 ADR)
- Система категорий важна для организации
- Регулярная очистка superseded ADR

### Большие проекты (50+ ADR)
- Нужно продвинутое taggging и filtering
- Рассмотрите retention policies для старых ADR
- Search становится критичным

---

## Миграция с Legacy ADR

### Распространённые Legacy паттерны

**Разрозненные документы решений**:
- Design docs в различных форматах и местах
- Wiki страницы с устаревшим rationale решений
- Комментарии в коде с объяснениями дизайна

**Дублирующийся контент**:
- Одна информация в нескольких файлах/местах
- Разные версии одного документа

### Стратегия миграции

#### Фаза 1: Assessment
```bash
# Inventory существующей документации
find . -name "*.md" | grep -E "(design|decision|architecture|adr)"

# Идентификация дубликатов
find . -name "*.md" -exec basename {} \; | sort | uniq -d
```

#### Фаза 2: Triage
- **Keep and Migrate**: Ясные архитектурные выборы с rationale
- **Archive**: Старые design docs, которые информировали прошлые решения
- **Consolidate**: Одна информация в нескольких местах
- **Discard**: Устаревшие, заменённые, нерелевантные

#### Фаза 3: Execute
1. Verify перед удалением (diff файлы)
2. Проверить ссылки на файлы, которые перемещаются
3. Обновить cross-references
4. Задокументировать изменения

### Безопасный процесс миграции

```bash
# Всегда проверяйте идентичность файлов перед удалением
diff file1.md file2.md

# Проверить любые ссылки
grep -r "file1.md" . --exclude-dir=.git
```

---

## Метрики успеха

### Индикаторы adoption
- ADR создаются для значимых решений
- Регулярные обновления во время разработки
- Ссылки на ADR в code reviews

### Индикаторы качества
- ADR содержат достаточный контекст для воссоздания решения
- Implementation соответствует задокументированным решениям
- Lessons learned зафиксированы

### Индикаторы ценности
- Более быстрое onboarding новых членов команды
- Меньше пересмотра прошлых решений
- Лучший анализ влияния для предлагаемых изменений

---

## Точки интеграции

- **Git Workflows**: git-flow, GitHub Flow, GitLab flow
- **Issue Tracking**: GitHub Issues, Jira, Linear
- **Code Review**: Ссылки на ADR в описаниях PR
- **Documentation**: Ссылки из README, architecture docs
