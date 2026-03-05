# Traversal State

> Персистентный стек рекурсии для обхода дерева. AI читает это чтобы знать где он и что делать дальше.

## Mode

- **BFS** (no comment): Breadth-first, анализировать все domains систематически
- **DFS** (with comment): Depth-first, фокус глубоко на конкретной теме

## Source Path

[корень проекта]

## Focus (DFS only)

[none]

## Algorithm

```
RECURSIVE-UNDERSTAND(node):
    1. ENTER: Push node в стек, установить phase = ENTERING
    2. EXPLORE: Прочитать код, сформировать понимание, установить phase = EXPLORING
    3. SPAWN: Идентифицировать детей (более глубокие концепции), установить phase = SPAWNING
    4. RECURSE: Для каждого ребёнка -> RECURSIVE-UNDERSTAND(child)
    5. SYNTHESIZE: Комбинировать insights детей, установить phase = SYNTHESIZING
    6. EXIT: Pop из стека, bubble up summary, установить phase = EXITING
```

## Current Stack

> Читать сверху-вниз = root-к-current. Последний элемент = где AI сейчас.

```
[EMPTY - не запущено]
```

Пример во время запуска:
```
/ (root)                           DONE
└── core-domain                    DONE
    └── authentication             EXPLORING ← current
        └── token-management       PENDING
```

## Stack Operations Log

| # | Operation | Node | Phase | Result |
|---|-----------|------|-------|--------|
| - | - | - | - | - |

## Current Position

- **Node**: [none]
- **Phase**: IDLE | ENTERING | EXPLORING | SPAWNING | SYNTHESIZING | EXITING
- **Depth**: 0
- **Path**: /

## Pending Children

> Дети идентифицированы но ещё не исследованы (LIFO - последний добавленный исследуется первым)

```
[none]
```

## Visited Nodes

> Завершённые nodes с их summary

| Node Path | Summary | Flow Created |
|-----------|---------|--------------|
| - | - | - |

## Next Action

```
1. [Start: Push root node, начать ENTERING phase]
```

---

## Определения фаз

### ENTERING
- Только что прибыли в этот node
- Создать файл _node.md
- Прочитать релевантные исходные файлы
- Сформировать начальную гипотезу

### EXPLORING
- Глубокий анализ scope этого node
- Валидировать/уточнить гипотезу
- Идентифицировать что принадлежит здесь vs детям

### SPAWNING
- Идентифицировать дочерние концепции требующие более глубокого исследования
- Добавить детей в Pending стек
- Дети это ЛОГИЧЕСКИЕ концепции, не filesystem paths

### SYNTHESIZING
- Все дети завершены (или нет детей)
- Комбинировать insights от детей
- Обновить _node.md этого node с полным пониманием

### EXITING
- Pop из стека
- Bubble up summary к родителю
- Пометить как visited

---

## Протокол возобновления

Когда `/legacy` стартует:
1. Прочитать _traverse.md
2. Найти текущую позицию (вершина стека)
3. Проверить фазу
4. Продолжить с этой фазы

Если прервано mid-phase:
- Re-enter в ту же фазу (идемпотентные операции)

---

*Обновлено /legacy recursive traversal*
