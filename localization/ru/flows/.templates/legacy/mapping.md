# Code to Flow Mapping

## Overview

Maps analyzed code modules к сгенерированным flows.

## Flow Type Detection Rules

| Indicator | Flow Type |
|-----------|-----------|
| `*.test.*`, `*.spec.*`, `__tests__/` | TDD |
| `components/`, `*.tsx`, `*.vue`, `templates/` | VDD |
| `README.md`, public exports, API docs | DDD |
| Internal logic, no UI, no public API | SDD |

## Mapping Table

| Code Path | Flow | Type | Action | Status | Notes |
|-----------|------|------|--------|--------|-------|
| - | - | - | CREATED/UPDATED/UNCHANGED/CONFLICT | DRAFT | - |

### Action Values
- **CREATED** - Новый flow создан
- **UPDATED** - Существующий flow дополнен (только additive changes)
- **UNCHANGED** - Flow существует, новой информации не найдено
- **CONFLICT** - Анализ противоречит существующей документации (нужна reconciliation)

## ADR Mapping

| Code Pattern | ADR | Type | Status |
|--------------|-----|------|--------|
| - | - | constraining/enabling | DRAFT |

## Unmapped (нужен manual review)

| Code Path | Reason |
|-----------|--------|
| - | - |

---

*Auto-generated. Обновлять по мере анализа.*
