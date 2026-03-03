# Waterfall - BFS Flow Orchestration

Complete breadth-first development: document by features, implement by layers.

## First Run: Initialize from Templates

Check if `flows/waterfall/` exists. If not, copy from `flows/.templates/waterfall/`.

## Command: $ARGUMENTS

```
/waterfall                    # Full BFS - all flows, layered implementation
/waterfall status             # Show current state without executing
```

## Core Principle: Document by FEATURES, Implement by LAYERS

```
DOCUMENTATION (by features):
  Phase 1: ALL Requirements
  Phase 2: ALL Specifications
  Phase 3: ALL Plans

OPTIMIZATION:
  Phase 4: Layer Extraction    ← group tasks by layer
  Phase 5: Master Plan         ← order by layer

IMPLEMENTATION (by layers):
  Phase 6: L0 → L1 → L2        ← bottom-up
```

---

## Why Layer Extraction?

```
WITHOUT layers:                WITH layers:
  Feature A: [auth][api][ui]     L0: [config][types]
  Feature B: [auth][api][ui]     L1: [auth×3][api×3]  ← grouped!
  Feature C: [auth][api][ui]     L2: [ui×3]

  3× context switches            1× per module
  3× potential conflicts         consistent patterns
```

---

## Architectural Layers

```
Layer 2: FEATURE     ← UI, handlers (last)
Layer 1: DOMAIN      ← Business logic, API
Layer 0: SHARED      ← Config, types, DB (first)
```

---

## Status Synchronization

**CRITICAL**: Every status change updates TWO places:

```
flows/waterfall/_status.md  ←sync→  flows/[type]-[name]/_status.md
```

---

## Always

- Complete current BFS phase for ALL flows before advancing
- Document by FEATURE, implement by LAYER
- SYNC status to BOTH waterfall and individual flow
- Ask approval at phase transitions
