# Waterfall - BFS Flow Orchestration

Complete breadth-first development: all documentation before any implementation.

## First Run: Initialize from Templates

**Before any execution**, check if `flows/waterfall/` exists:

```
IF flows/waterfall/ does NOT exist:
  1. Copy flows/.templates/waterfall/ → flows/waterfall/
  2. Inform user: "Initialized waterfall workspace from templates"
  3. Continue with execution
```

## Command: $ARGUMENTS

```
/waterfall                    # Full BFS - all flows, all details
/waterfall status             # Show current state without executing
```

## Core Principle: BFS (Breadth-First) + Layered Implementation

**Document by FEATURES, implement by LAYERS.**

```
DOCUMENTATION (by features):
  Phase 1: ALL Requirements    → understand business needs per feature
  Phase 2: ALL Specifications  → define interfaces per feature
  Phase 3: ALL Plans           → plan tasks per feature

OPTIMIZATION:
  Phase 4: Layer Extraction    → group tasks by architectural layer
  Phase 5: Master Plan         → optimize execution order

IMPLEMENTATION (by layers):
  Phase 6: Implementation      → execute bottom-up by layer
```

This ensures:
- Business context preserved during planning
- Implementation grouped by module (less context switching)
- Shared code written once, used by all features
- Maximum code reuse, minimum entropy

---

## Why Layer Extraction?

```
WITHOUT Layer Extraction (by feature):
  Feature A: [auth-1] [api-1] [ui-1]
  Feature B: [auth-2] [api-2] [ui-2]
  Feature C: [auth-3] [api-3] [ui-3]

  Problem: 3× context switches in auth module
           3× potential conflicts
           3× duplicate patterns

WITH Layer Extraction (by layer):
  Layer 0 (Shared):  [config] [utils] [types]
  Layer 1 (Domain):  [auth-1,2,3] [api-1,2,3]  ← grouped!
  Layer 2 (Feature): [ui-1] [ui-2] [ui-3]

  Benefit: 1× immersion in auth module
           Consistent patterns
           Shared code extracted
```

---

## Architectural Layers

```
┌─────────────────────────────────────────────────────────┐
│                    LAYER HIERARCHY                       │
├─────────────────────────────────────────────────────────┤
│                                                         │
│  Layer 2: FEATURE                                       │
│  ┌─────────┐ ┌─────────┐ ┌─────────┐                   │
│  │Feature A│ │Feature B│ │Feature C│  Feature-specific │
│  └────┬────┘ └────┬────┘ └────┬────┘  UI, handlers     │
│       │           │           │                         │
│       ▼           ▼           ▼                         │
│  Layer 1: DOMAIN                                        │
│  ┌─────────────────────────────────────┐               │
│  │  Auth  │  Users  │  API  │  Events │  Business logic│
│  └─────────────────────────────────────┘               │
│                      │                                  │
│                      ▼                                  │
│  Layer 0: SHARED/INFRASTRUCTURE                         │
│  ┌─────────────────────────────────────┐               │
│  │ Config │ Utils │ Types │ DB │ Logger│  Shared code  │
│  └─────────────────────────────────────┘               │
│                                                         │
└─────────────────────────────────────────────────────────┘

IMPLEMENTATION ORDER: Layer 0 → Layer 1 → Layer 2 (bottom-up)
```

---

## BFS Execution Flow

```
┌─────────────────────────────────────────────────────────────────┐
│                      BFS WATERFALL                               │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌─────────┐  ┌─────────┐  ┌─────────┐  ┌─────────┐           │
│  │Feature A│  │Feature B│  │Feature C│  │Feature D│           │
│  └────┬────┘  └────┬────┘  └────┬────┘  └────┬────┘           │
│       │            │            │            │                 │
│       ▼            ▼            ▼            ▼                 │
│  ╔═══════════════════════════════════════════════════╗         │
│  ║        PHASE 1-3: DOCUMENTATION (by feature)      ║         │
│  ║   REQ-A ✓    REQ-B ✓    REQ-C ✓    REQ-D ✓       ║         │
│  ║  SPEC-A ✓   SPEC-B ✓   SPEC-C ✓   SPEC-D ✓       ║         │
│  ║  PLAN-A ✓   PLAN-B ✓   PLAN-C ✓   PLAN-D ✓       ║         │
│  ╚═══════════════════════════════════════════════════╝         │
│                          │                                     │
│                          ▼                                     │
│  ╔═══════════════════════════════════════════════════╗         │
│  ║           PHASE 4: LAYER EXTRACTION               ║         │
│  ║                                                   ║         │
│  ║  Tasks from ALL plans:                            ║         │
│  ║    [A1][A2][A3] [B1][B2] [C1][C2][C3] [D1][D2]   ║         │
│  ║                     │                             ║         │
│  ║                     ▼                             ║         │
│  ║  Grouped by layer:                                ║         │
│  ║    L0 (shared):  [A1][C1]                        ║         │
│  ║    L1 (domain):  [A2][B1][C2][D1]                ║         │
│  ║    L2 (feature): [A3][B2][C3][D2]                ║         │
│  ╚═══════════════════════════════════════════════════╝         │
│                          │                                     │
│                          ▼                                     │
│  ╔═══════════════════════════════════════════════════╗         │
│  ║           PHASE 5: MASTER PLAN                    ║         │
│  ║     Execution order by layer (bottom-up)          ║         │
│  ╚═══════════════════════════════════════════════════╝         │
│                          │                                     │
│                          ▼                                     │
│  ╔═══════════════════════════════════════════════════╗         │
│  ║           PHASE 6: IMPLEMENTATION                 ║         │
│  ║                                                   ║         │
│  ║  Execute L0: [A1] → [C1]           (shared)      ║         │
│  ║  Execute L1: [A2] → [B1] → [C2] → [D1] (domain)  ║         │
│  ║  Execute L2: [A3] → [B2] → [C3] → [D2] (feature) ║         │
│  ╚═══════════════════════════════════════════════════╝         │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

---

## Execution Steps

### Step 1: Analyze All Flows

```
1. Read flows/waterfall/_status.md
2. Scan all flows/sdd-*/, flows/ddd-*/, flows/tdd-*/, flows/vdd-*/
3. Read each _status.md and all documents
4. Read flows/adr-*/ for context
5. Build complete dependency graph
6. Update flows/waterfall/dependencies.md
```

### Step 2: Show Complete Overview

Always display before proceeding:

```
=== WATERFALL BFS STATUS ===

Flows Discovered: 4
  ○ sdd-auth (REQ: draft, SPEC: none, PLAN: none)
  ○ ddd-dashboard (REQ: approved, SPEC: draft, PLAN: none)
  ○ tdd-validation (REQ: approved, SPEC: approved, PLAN: draft)
  ○ sdd-api (REQ: none, SPEC: none, PLAN: none)

Current Phase: REQUIREMENTS (1/6)
  Approved: 2/4
  Drafting: 1/4
  Missing: 1/4

Dependencies:
  sdd-auth ──blocks──> sdd-api
  sdd-api ──blocks──> ddd-dashboard

Next: Complete requirements for sdd-auth, sdd-api

=============================
```

### Step 3: Execute Documentation Phases (1-3)

**Phase 1 - Requirements (by feature):**
```
FOR each flow without approved requirements:
  1. Draft/review requirements
  2. Ask user: "requirements approved?"
  3. SYNC status to both waterfall/_status.md AND flow/_status.md
  4. Continue until ALL requirements approved
```

**Phase 2 - Specifications (by feature):**
```
FOR each flow without approved specifications:
  1. Draft/review specifications
  2. Ask user: "specs approved?"
  3. SYNC status
  4. Continue until ALL specifications approved
```

**Phase 3 - Plans (by feature):**
```
FOR each flow without approved plan:
  1. Draft/review plan
  2. Ask user: "plan approved?"
  3. SYNC status
  4. Continue until ALL plans approved
```

### Step 4: Layer Extraction

**Extract and classify all tasks from all approved plans:**

```
1. Read all approved plans
2. Extract every task with metadata:
   - Task ID
   - Source flow
   - Description
   - Dependencies

3. Classify each task into layer:

   LAYER 0 - SHARED/INFRASTRUCTURE:
   - Database schemas, migrations
   - Configuration, environment
   - Shared utilities, helpers
   - Common types, interfaces
   - Logging, monitoring setup

   LAYER 1 - DOMAIN/CORE:
   - Business logic
   - Domain services
   - API endpoints
   - Data access
   - Validation rules

   LAYER 2 - FEATURE:
   - UI components
   - Feature-specific handlers
   - Integration code
   - Feature tests

4. Within each layer, group by MODULE:
   Layer 1 tasks grouped: [auth/*] [users/*] [api/*]

5. Write to flows/waterfall/layers.md
6. Ask user: "layer extraction approved?"
```

### Step 5: Master Plan

```
1. Read flows/waterfall/layers.md
2. Order tasks:
   - Layer 0 first (all shared code)
   - Layer 1 second (grouped by module)
   - Layer 2 last (feature-specific)
3. Within each layer, respect dependencies
4. Identify parallelization opportunities
5. Create flows/waterfall/master-plan.md
6. Ask user: "master plan approved?"
```

### Step 6: Implementation

```
Execute tasks in master plan order (by layer):

FOR each layer (0 → 1 → 2):
  FOR each module in layer:
    FOR each task in module:
      1. Execute task
      2. Update implementation-log.md in relevant flow
      3. SYNC status to waterfall AND flow
      4. Continue to next task
```

---

## Layer Classification Guide

### Layer 0: Shared/Infrastructure

| Pattern | Example | Why Layer 0 |
|---------|---------|-------------|
| Database schema | `CREATE TABLE users` | Used by all features |
| Config files | `.env`, `config.ts` | Global settings |
| Shared types | `interface User` | Multiple features need |
| Utilities | `formatDate()` | Generic helpers |
| Base classes | `BaseRepository` | Inherited by domain |

### Layer 1: Domain/Core

| Pattern | Example | Why Layer 1 |
|---------|---------|-------------|
| Business logic | `AuthService.login()` | Core functionality |
| API routes | `POST /api/users` | Domain operations |
| Repositories | `UserRepository` | Data access |
| Validators | `validateEmail()` | Business rules |
| Events | `UserCreatedEvent` | Domain events |

### Layer 2: Feature

| Pattern | Example | Why Layer 2 |
|---------|---------|-------------|
| UI components | `LoginForm.tsx` | Feature-specific |
| Page handlers | `DashboardPage` | Feature entry |
| Feature tests | `login.e2e.test` | Feature validation |
| Integration | `connectToPayment()` | Feature-specific |

---

## Status Synchronization

**CRITICAL**: Every status change updates TWO places:

```
┌────────────────────┐        ┌────────────────────┐
│ flows/waterfall/   │  sync  │ flows/[type]-[x]/  │
│   _status.md       │◄──────►│   _status.md       │
└────────────────────┘        └────────────────────┘
```

### Sync Protocol

```
SYNC(flow-name, artifact, status):
  1. Update flows/[type]-[flow-name]/_status.md
     - Set artifact status
     - Update progress checklist
     - Set current phase if advancing

  2. Update flows/waterfall/_status.md
     - Update flow tracker
     - Update phase progress
     - Update overall statistics

  3. Update flows/waterfall/log.md
     - Log: "[timestamp] [flow-name] [artifact] → [status]"
```

---

## Directory Structure

```
flows/waterfall/
├── _status.md              # Overall BFS progress + per-flow tracker
├── dependencies.md         # Flow dependency graph
├── layers.md               # Task classification by layer
├── master-plan.md          # Execution order (by layer)
└── log.md                  # All status changes and actions
```

---

## _status.md Structure

```markdown
# Waterfall Status

## Mode: BFS

## Current Phase

REQUIREMENTS | SPECIFICATIONS | PLANS | LAYER_EXTRACTION | MASTER_PLAN | IMPLEMENTATION | COMPLETE

## Phase Progress

### Documentation (by feature)

| Phase | Complete | Total | Status |
|-------|----------|-------|--------|
| Requirements | 2 | 4 | IN_PROGRESS |
| Specifications | 0 | 4 | PENDING |
| Plans | 0 | 4 | PENDING |

### Optimization

| Phase | Status |
|-------|--------|
| Layer Extraction | PENDING |
| Master Plan | PENDING |

### Implementation (by layer)

| Layer | Complete | Total | Status |
|-------|----------|-------|--------|
| L0 Shared | 0 | ? | PENDING |
| L1 Domain | 0 | ? | PENDING |
| L2 Feature | 0 | ? | PENDING |

## Overall Progress

- Phase 1 (REQ): 2/4 complete
- Phase 2 (SPEC): 0/4 complete
- Phase 3 (PLAN): 0/4 complete
- Phase 4 (LAYERS): not started
- Phase 5 (MASTER): not started
- Phase 6 (IMPL): not started

## Last Action

[timestamp] Approved requirements for sdd-auth

## Next Action

1. Complete requirements for sdd-api
```

---

## Comparison with DFS (/roadmap)

| Aspect | /waterfall (BFS) | /roadmap (DFS) |
|--------|------------------|----------------|
| Goal | Complete everything | Reach specific target |
| Documentation | All features documented | Only critical path |
| Implementation | By layer (optimized) | By feature (fast) |
| When | Full project | MVP or specific goal |
| Result | Comprehensive + efficient | Minimum viable path |

---

## Always

- Complete current BFS phase for ALL flows before advancing
- Document by FEATURE, implement by LAYER
- SYNC status to BOTH waterfall and individual flow
- Show overview before each major action
- Ask approval at phase transitions
- Log everything to log.md
- Never skip phases
