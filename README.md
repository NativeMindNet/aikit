# AIKit

**Document-Driven Development framework for AI-assisted software development.**

AIKit is a project template that treats documentation as the primary development artifact. Code becomes the "last-mile" implementation derived from well-defined documents.

[🇷🇺 Русская версия](./README_ru.md)

## Why This Template?

This template is designed for effective collaboration with AI assistants (Claude, Cursor, Qwen, Gemini). It solves the following problems:

| Problem | AIKit Solution |
|---------|----------------|
| **Context loss between sessions** | Structured documents allow resuming work without re-explanation |
| **Unclear why a decision was made** | ADRs (Architecture Decision Records) capture decisions with rationale |
| **AI "reinvents" solutions** | Requirements and specifications are already documented |
| **Hard to track progress** | Each phase has status and checklist |
| **Different approaches for different tasks** | 4 workflows (SDD/DDD/TDD/VDD) for different scenarios |

### Key Benefits

- **Resumability** — Continue work across sessions without context loss
- **Traceability** — Every implementation decision traces back to requirements
- **Iteration** — Refine documents before writing code
- **Parallelization** — Multiple AI agents can work from the same documents
- **Communication** — Documentation for clients and executives (in DDD)
- **Decision History** — ADRs capture architectural decisions with full rationale

## Project Structure

```
aikit/
├── README.md                 # This file — project overview (English)
├── README_ru.md              # Russian version of this file
├── aikit-en/                 # English localization
│   ├── README.md             # Full documentation in English
│   ├── CLAUDE.md             # Auto-loaded context
│   ├── .claude/commands/     # Commands for Claude Code
│   ├── .cursor/prompts/      # Commands for Cursor
│   ├── .qwen/commands/       # Commands for Qwen
│   └── flows/                # Flow templates and instances
├── aikit-ru/                 # Russian localization
│   ├── README.md             # Full documentation in Russian
│   ├── CLAUDE.md             # Auto-loaded context
│   ├── .claude/commands/     # Commands for Claude Code
│   ├── .cursor/prompts/      # Commands for Cursor
│   ├── .qwen/commands/       # Commands for Qwen
│   └── flows/                # Flow templates and instances
└── 3rdparty/                 # Third-party solutions and documentation
    ├── adr/                  # ADR system (Architecture Decision Records)
    ├── sdd/                  # Spec-Driven Development resources
    ├── speckit/              # Specification tools
    └── vsdd/                 # Visual-Driven Development resources
```

### Localization

The project supports two languages:

| Language | Folder | Documentation |
|----------|--------|---------------|
| 🇬🇧 English | `aikit-en/` | [`aikit-en/README.md`](./aikit-en/README.md) |
| 🇷🇺 Русский | `aikit-ru/` | [`aikit-ru/README.md`](./aikit-ru/README.md) |

Both versions contain the same structure and functionality. Choose the folder that matches your preferred language.

## Workflows

| Flow | When to Use | Phases |
|------|-------------|--------|
| **SDD** (Spec-Driven) | Internal features, infrastructure, developer tooling | Requirements → Specs → Plan → Implementation |
| **DDD** (Document-Driven) | Client-facing features, require stakeholder buy-in | Requirements → Specs → Plan → Implementation → Documentation |
| **TDD** (Tests-Driven) | Financial calculations, security logic, complex state machines | Requirements → Tests → Specs → Plan → Implementation → Documentation |
| **VDD** (Visual-Driven) | UI/UX is the primary concern | Requirements → Visual → Specs → Plan → Implementation → Documentation |

## Quick Start

1. **Choose your localization folder** (`aikit-en/` or `aikit-ru/`)
2. **Copy the structure** to your project
3. **Start a flow**:
   ```bash
   /sdd start [feature-name]    # Internal feature
   /ddd start [feature-name]    # Client-facing feature
   /tdd start [feature-name]    # Correctness-critical
   /vdd start [feature-name]    # UI-primary
   ```
4. **Work through phases** with explicit approval at each stage

## Orchestration

### Roadmap (DFS)
Depth-first — completes blocking dependencies one at a time.

```bash
/roadmap         # DFS to MVP
/roadmap [goal]  # DFS to specific goal
```

### Waterfall (BFS)
Breadth-first — documents everything, then compiles into layers.

```bash
/waterfall        # BFS: Document all, then implement
/waterfall compile  # Compile layers from approved plans
```

## Supported IDEs

| IDE | Commands Folder |
|-----|-----------------|
| Claude Code | `.claude/commands/` |
| Cursor | `.cursor/prompts/commands/` |
| Qwen | `.qwen/commands/` |
| Gemini | `.gemini/commands/` |

## Additional Resources

- **ADR Guide**: [`3rdparty/adr/adr.md`](./3rdparty/adr/adr.md) — comprehensive guide to Architecture Decision Records
- **English Documentation**: [`aikit-en/README.md`](./aikit-en/README.md)
- **Russian Documentation**: [`aikit-ru/README.md`](./aikit-ru/README.md)

---

**AIKit** — Documentation as the primary artifact. Code as the last-mile implementation.
