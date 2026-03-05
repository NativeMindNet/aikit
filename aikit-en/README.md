# Document-Driven Development Template

A comprehensive framework for managing software development through structured documentation. This template supports **SDD**, **DDD**, **TDD**, **VDD** workflows, **ADR** (Architecture Decision Records), and orchestration tools (**roadmap**, **legacy**).

## Overview

This template treats documentation as the primary development artifact, where code is the "last-mile" implementation derived from well-defined documents. It provides:

- **Resumability**: Continue work across sessions without context loss
- **Traceability**: Every implementation decision traces back to requirements
- **Iteration**: Refine documents before committing to code
- **Parallelization**: Multiple agents can work from the same documents
- **Stakeholder Communication**: Feature presentations for clients and executives
- **Decision History**: ADRs capture architectural decisions with full rationale

## Auto-loaded Context

The `CLAUDE.md` file is automatically loaded at session start, providing:
- ADR Index Summary (all decisions with status and links)
- Active flows list (SDD/DDD/TDD/VDD in progress)
- Quick navigation to documentation and commands

## Supported Workflows

### SDD (Spec-Driven Development)
Internal features, developer-focused. 4 phases:

```
REQUIREMENTS → SPECIFICATIONS → PLAN → IMPLEMENTATION
```

**Use when**: Internal logic, infrastructure, developer tooling.

### DDD (Document-Driven Development)
Features requiring **stakeholder buy-in**. 5 phases:

```
REQUIREMENTS → SPECIFICATIONS → PLAN → IMPLEMENTATION → DOCUMENTATION
```

**Use when**: Client-facing features, need to "sell" the feature, documentation is deliverable.

**Key difference**: Final phase creates a **mini-presentation** for clients/executives - not technical docs, but value explanation.

### TDD (Tests-Driven Development)
**Cases-first** approach for correctness-critical features. 6 phases:

```
REQUIREMENTS → TESTS → SPECIFICATIONS → PLAN → IMPLEMENTATION → DOCUMENTATION
```

**Use when**: Financial calculations, security logic, complex state machines.

**Key difference**: TESTS phase is exhaustive behavioral analysis. Design **emerges from cases**, not invented independently. Specs are **derived from tests**.

### VDD (Visual-Driven Development)
UI/UX-primary features. 6 phases:

```
REQUIREMENTS → VISUAL → SPECIFICATIONS → PLAN → IMPLEMENTATION → DOCUMENTATION
```

**Use when**: User interfaces, visual design is primary concern.

**Key difference**: VISUAL phase defines UI before technical specs.

## Quick Start

### 1. Choose Your IDE
This template supports multiple AI-powered IDEs:
- **Claude Code**: Commands in `.claude/commands/`
- **Cursor**: Commands in `.cursor/prompts/commands/`
- **Qwen**: Commands in `.qwen/commands/`
- **Gemini**: Commands in `.gemini/commands/`

### 2. Start a New Flow

```
/sdd start [feature-name]    # Internal feature
/ddd start [feature-name]    # Client-facing feature
/tdd start [feature-name]    # Correctness-critical
/vdd start [feature-name]    # UI-primary
```

### 3. Work Through Phases

Each phase requires explicit approval before advancing:
- "requirements approved"
- "tests approved" (TDD) / "visual approved" (VDD)
- "specs approved"
- "plan approved"
- "docs approved" (DDD/TDD/VDD)

## Project Structure

```
.
├── CLAUDE.md                     # Auto-loaded context (ADR index, flows list)
├── flows/
│   ├── sdd.md                    # SDD flow documentation
│   ├── ddd.md                    # DDD flow documentation
│   ├── tdd.md                    # TDD flow documentation
│   ├── vdd.md                    # VDD flow documentation
│   ├── adr.md                    # ADR flow documentation
│   ├── adr-index.md              # Master index of all ADRs
│   ├── .templates/               # Templates for new flows
│   │   ├── sdd/
│   │   ├── ddd/
│   │   ├── tdd/
│   │   ├── vdd/
│   │   └── adr/
│   ├── sdd-[feature]/            # SDD flow instances
│   ├── ddd-[feature]/            # DDD flow instances
│   ├── tdd-[feature]/            # TDD flow instances
│   ├── vdd-[feature]/            # VDD flow instances
│   ├── adr-[NNN]-[name]/         # ADR instances (numbered)
│   ├── roadmap/                  # Flow orchestration
│   │   ├── _status.md
│   │   ├── dependencies.md
│   │   ├── plan.md
│   │   └── log.md
│   └── legacy/                   # Reverse engineering
│       ├── _status.md
│       ├── log.md
│       ├── analysis/
│       ├── mapping.md
│       └── review.md
│
├── .claude/commands/             # Claude Code commands
├── .cursor/prompts/commands/     # Cursor commands
├── .qwen/commands/               # Qwen commands
└── .gemini/commands/             # Gemini commands
```

## Available Commands

### Core Workflows

| Command | Description |
|---------|-------------|
| `/sdd start [name]` | Start new SDD flow (internal feature) |
| `/ddd start [name]` | Start new DDD flow (client-facing) |
| `/tdd start [name]` | Start new TDD flow (correctness-critical) |
| `/vdd start [name]` | Start new VDD flow (UI-primary) |
| `/[flow] resume [name]` | Resume existing flow |
| `/[flow] status` | Show all active flows of type |

### Architecture Decision Records

| Command | Description |
|---------|-------------|
| `/adr start [name]` | Start new ADR in DRAFT phase |
| `/adr resume [n]` | Resume existing ADR |
| `/adr quick [name]` | Create lightweight ADR |
| `/adr list` | Show all ADRs with status |
| `/adr.guide` | Reference: language patterns, scaling, migration |

**ADR Types**:
- `constraining` - selects from options, narrows scope
- `enabling` - adds capabilities, expands scope

**Flow**: `DRAFT → REVIEW → APPROVED | REJECTED`

### Roadmap - Flow Orchestration

| Command | Description |
|---------|-------------|
| `/roadmap` | BFS: Document all flows, then implement |
| `/roadmap [flow]` | DFS: Complete specific flow + blockers |
| `/roadmap status` | Show current state |

**BFS Mode** (no args):
- Documents all flows to PLAN approved
- Builds master implementation plan
- Executes by consolidated plan

**DFS Mode** (with flow):
- Finds blocking dependencies recursively
- Completes blockers depth-first
- Completes target flow

### Legacy - Reverse Engineering

| Command | Description |
|---------|-------------|
| `/legacy` | Analyze entire project, generate docs |
| `/legacy [path]` | Analyze specific path |
| `/legacy [path] "focus"` | DFS: Focus on specific functionality |

**Module-first detection** - flows per module, not per file type.

**Auto-detects flow type by purpose**:
- SDD ← internal logic, no stakeholder communication needed
- DDD ← requires client/executive explanation
- TDD ← correctness-critical, tests define behavior
- VDD ← UI/UX is primary concern

**Idempotent**: Appends to existing flows, no duplicates.

All generated flows are DRAFT status, require review.

## Choosing the Right Flow

| Situation | Flow |
|-----------|------|
| Internal refactoring | SDD |
| Developer tooling | SDD |
| Infrastructure | SDD |
| Client-facing feature | DDD |
| Feature needs "selling" to stakeholders | DDD |
| Documentation is deliverable | DDD |
| Financial/security calculations | TDD |
| Complex state machines | TDD |
| Behavior must be exhaustively defined | TDD |
| UI/UX is the primary concern | VDD |
| Visual design drives development | VDD |

## Phase Transitions

Each phase requires explicit user approval:

| Transition | Approval phrase |
|------------|-----------------|
| Requirements → next | "requirements approved" |
| Tests → Specs (TDD) | "tests approved" |
| Visual → Specs (VDD) | "visual approved" |
| Specs → Plan | "specs approved" |
| Plan → Implementation | "plan approved" |
| Implementation → Docs | "ready for docs" |
| Docs → Complete | "docs approved" |

## Status Tracking

Each flow maintains `_status.md`:
- Current phase and status
- Progress checklist
- Blockers
- Context notes for resuming
- Next actions

## Best Practices

### Do
- Update `_status.md` after significant changes
- Wait for explicit approval before phase transitions
- Use TDD for correctness-critical logic
- Use DDD when stakeholder buy-in needed
- Think module-first, not file-type-first

### Don't
- Skip phases
- Create separate flows for tests/UI of a module
- Assume approval without confirmation
- Use technical jargon in DDD documentation phase
- Run /legacy without reviewing results

## Session Handoff

When ending mid-flow:
1. Update `_status.md` with current state
2. Document in-flight reasoning
3. List explicit next steps

New sessions read `_status.md` first to reconstruct context.

---

**Note**: This template is designed for AI-assisted development. The structured approach enables AI agents to maintain context across sessions and deliver traceable implementations.
