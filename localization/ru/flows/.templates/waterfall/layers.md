# Layer Compilation Index

## Compilation Status

| Layer | File | Status | Last Compiled | Tasks | Gaps |
|-------|------|--------|---------------|-------|------|
| 0 | layer-0.md | NOT_COMPILED | - | 0 | 0 |
| 1 | layer-1.md | NOT_COMPILED | - | 0 | 0 |
| 2 | layer-2.md | NOT_COMPILED | - | 0 | 0 |

**Overall:** NOT_COMPILED

---

## Source Flows

| Flow | Type | Plan Status | Included In |
|------|------|-------------|-------------|
| - | - | - | - |

---

## Compilation Process

```
1. Прочитать все approved plans из source flows
2. Извлечь tasks с метаданными
3. Классифицировать в layers (0/1/2)
4. Detect gaps и conflicts
5. Если gaps: stop, resolve в source, retry
6. Сгенерировать layer-N.md файлы
7. Обновить этот index
```

---

## Layer Architecture

```
┌─────────────────────────────────────────┐
│ Layer 2: FEATURE                        │
│   UI, handlers, feature-specific        │
│   Depends on: Layer 0, Layer 1          │
├─────────────────────────────────────────┤
│ Layer 1: DOMAIN                         │
│   Business logic, API, services         │
│   Depends on: Layer 0                   │
├─────────────────────────────────────────┤
│ Layer 0: SHARED                         │
│   Config, types, DB, utils              │
│   Depends on: nothing                   │
└─────────────────────────────────────────┘

Implementation order: 0 → 1 → 2 (bottom-up)
```

---

## Classification Rules

### Layer 0: Shared/Infrastructure
- Database schemas, migrations
- Configuration, environment
- Shared types, interfaces
- Utilities, helpers
- Logging, monitoring setup

### Layer 1: Domain/Core
- Business logic, services
- API endpoints
- Repositories, data access
- Validation rules
- Domain events

### Layer 2: Feature
- UI components
- Page handlers
- Feature-specific integration
- E2E tests

---

## Gap Detection

Во время compilation проверять:

1. **TYPE CONFLICTS** — Same type defined differently
2. **MISSING DEPS** — Task references undefined item
3. **DUPLICATES** — Same functionality в multiple flows
4. **INTERFACE GAPS** — Layer N needs what Layer N-1 doesn't provide

Gaps записаны в: `gaps.md`

---

## Commands

```
/waterfall compile    # Run compilation
/waterfall gaps       # Show unresolved gaps
```

---

*Updated during compilation. Не редактировать вручную.*
