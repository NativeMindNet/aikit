# Layer 2: Feature

> **COMPILED from flows. Не редактировать напрямую.**
> Last compiled: [timestamp]
> Source flows: [list]

## Overview

- Total tasks: 0
- Features: 0
- Dependencies on Layer 0: [list]
- Dependencies on Layer 1: [list]

---

## Назначение

Feature-specific код:
- UI components
- Page handlers
- Feature-specific integration
- E2E tests

---

## Required from Layer 0

| Interface | Type | Provided By |
|-----------|------|-------------|
| - | - | layer-0/[module] |

## Required from Layer 1

| Interface | Type | Provided By |
|-----------|------|-------------|
| - | - | layer-1/[module] |

---

## Feature: [feature-name-a]

> Source: [flow-type]-[flow-name]

### Provided Interfaces

| Interface | Type | Description |
|-----------|------|-------------|
| - | - | - |

### Required Interfaces

| Interface | Type | From |
|-----------|------|------|
| - | - | layer-1 |

### Tasks

| # | Task ID | Description | Dependencies | Status |
|---|---------|-------------|--------------|--------|
| 1 | - | - | - | pending |

---

## Feature: [feature-name-b]

> Source: [flow-type]-[flow-name]

### Provided Interfaces

| Interface | Type | Description |
|-----------|------|-------------|
| - | - | - |

### Required Interfaces

| Interface | Type | From |
|-----------|------|------|
| - | - | layer-1 |

### Tasks

| # | Task ID | Description | Dependencies | Status |
|---|---------|-------------|--------------|--------|
| 1 | - | - | - | pending |

---

## Cross-Feature Dependencies

```
[feature-a]/[task] ──> [feature-b]/[task]
```

---

## Execution Order

Features могут часто быть parallelized если нет cross-dependencies:

```
Parallel group 1:
  - [feature-a]: [tasks]
  - [feature-b]: [tasks]

Sequential (depends on group 1):
  - [feature-c]: [tasks]
```

---

## Acceptance Criteria

- [ ] All UI components rendered
- [ ] Feature flows working E2E
- [ ] Integration tests passing
- [ ] User acceptance criteria met

---

*Compiled by /waterfall. Регенерировать с `/waterfall compile`*
