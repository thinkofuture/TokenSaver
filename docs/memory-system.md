# Context System Architecture

> How TokenSaver works: compressed first-pass context, file structure, linking, multi-tool entry points, and context window management.

---

## Core Concept: Compressed First-Pass

TokenSaver is not an extra layer on top of existing project configuration. It is a **compressed first-pass context layer** that replaces expensive full-repo scanning.

```
Without TokenSaver:
  Agent starts → scans entire repo → builds understanding → begins work
  Cost: ~15,000 tokens of scanning before any real work

With TokenSaver:
  Agent starts → reads TOKENSAVER.md → reads .tokensaver/ → begins work
  Cost: ~1,500 tokens of structured context

  Source docs and code are still the territory.
  TokenSaver is the compressed map.
```

---

## Three Layers

```
Layer 1: TOKENSAVER.md         ← Universal entry point (first-pass index)
    │
    ▼
Layer 2: Context Files         ← Compressed knowledge in .tokensaver/
    │
    ▼
Layer 3: Actual Code           ← The repository (source of truth, read on-demand)
```

---

## Layer 1: Entry Points

### TOKENSAVER.md (Core Entry Point — always created)

`TOKENSAVER.md` is the universal entry point. It tells agents that `.tokensaver/` is the compressed first-pass read — they should read it before scanning source code. Target 5–15 lines. Each block is wrapped in markers for clean insertion and removal.

```markdown
# TOKENSAVER.md

<!-- tokensaver:start -->
## TokenSaver — Compressed First-Pass Context

Read `.tokensaver/` before scanning source code.
Start with:
1. `.tokensaver/session-handoff.md`
2. `.tokensaver/overview.md`
3. `.tokensaver/codebase-map.md`

Use `.tokensaver/architecture.md`, `.tokensaver/conventions.md`, `.tokensaver/decisions.md`, and `.tokensaver/gotchas.md` when relevant.
Treat source code as the source of truth. TokenSaver files are a compressed guide, not a replacement for code.
Only inspect source files when implementation details are needed.
<!-- tokensaver:end -->
```

### Adapter Files (Optional — thin pointers)

Adapters exist solely to guide tools that don't natively read `TOKENSAVER.md`. They are **not created by default**. Each adapter block is wrapped in `<!-- tokensaver:start -->` / `<!-- tokensaver:end -->` for clean insertion, identification, and removal.

**CLAUDE.md** — only if the project already has one or the user asks:

```markdown
<!-- tokensaver:start -->
## TokenSaver
Read `TOKENSAVER.md` before scanning source code.
<!-- tokensaver:end -->
```

**AGENTS.md** — only if the project already has one or the user asks:

```markdown
<!-- tokensaver:start -->
## TokenSaver
Read `TOKENSAVER.md` before scanning source code.
<!-- tokensaver:end -->
```

**Cursor rules** — only if `.cursor/` exists or the user asks:

```markdown
---
description: Use TokenSaver compressed first-pass context
alwaysApply: true
---
<!-- tokensaver:start -->
Read `TOKENSAVER.md` before scanning source code.
<!-- tokensaver:end -->
```

**Critical:** Adapters never overwrite existing content. If a file already has content, the TokenSaver block is appended at the end between the marker comments.

### Handling Mature Projects

If the project already has `CLAUDE.md`, `AGENTS.md`, or other instruction files, TokenSaver evaluates them first:

- **Already short and clear?** TokenSaver creates a minimal `TOKENSAVER.md` that indexes existing files — agents read existing config plus the TokenSaver index. No duplication.
- **Too long or scattered?** TokenSaver distills and compresses into `.tokensaver/`, reducing total first-pass tokens. Adapters point tools to `TOKENSAVER.md` instead of scanning everything.

---

## Layer 2: Context Files

### Default Structure (7 files)

```
.tokensaver/
├── overview.md              # Project identity, tech stack summary
├── architecture.md          # System design, components, data flow
├── codebase-map.md          # Annotated directory tree
├── conventions.md           # Code style, naming, patterns
├── decisions.md             # Architecture Decision Records
├── gotchas.md               # Surprising behaviors, known issues
└── session-handoff.md       # In-progress work, next steps
```

### File Format

```markdown
# [Title]

> [One-sentence summary]

**Last updated:** YYYY-MM-DD

---

[Scannable content]

## Related
- [[other-file]] — brief reason
```

### Cardinality by Project Size

| Project Size | Core Files Needed | Optional Additions |
|-------------|-------------------|-------------------|
| <50 files | `overview`, `codebase-map`, `conventions` | `gotchas` |
| 50–200 files | All 7 core files | — |
| 200–1000+ files | All 7 core files | `data-models`, `api-contracts`, `dependencies` |
| Monorepo | Top-level map + per-package core | Per-package additions |

### `[[wikilinks]]`

Context files use wikilinks for cross-referencing: `[[architecture]]` connects to `architecture.md`. Provides navigation, context hints, and drift detection.

### File Size

- **Target:** 30–80 lines
- **Max:** 150 lines
- **Min:** 10 lines (merge if shorter)

---

## Layer 3: Actual Code

Context is a map, not the territory. AI agents still read source files — but they read fewer of them, and only when implementation details are needed. Context reduces the search space from "the entire repo" to "the relevant files."

---

## Context Window Management

### Typical Token Budget

For a medium project:

| What | Tokens |
|------|--------|
| TOKENSAVER.md | ~150 |
| overview.md | ~300 |
| codebase-map.md | ~400 |
| session-handoff.md | ~200 |
| **First pass (every session)** | **~1,050** |
| conventions.md (when writing code) | ~400 |
| architecture.md (when designing) | ~500 |
| **Peak (on-demand context loaded)** | **~2,000** |

Compare to full repo scan: **10,000–20,000 tokens**. ~85–90% reduction.

### Progressive Loading

```
Session Start (always — compressed first-pass):
  ├── TOKENSAVER.md
  ├── overview.md
  ├── codebase-map.md
  └── session-handoff.md

During Session (on-demand — only when needed):
  ├── conventions.md     — when writing code
  ├── architecture.md    — when designing changes
  ├── decisions.md       — when evaluating options
  └── gotchas.md         — when hitting issues
```

---

## Multi-Tool Architecture

```
                    ┌─────────────────┐
                    │   .tokensaver/   │
                    │  (compressed     │
                    │   context)       │
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
                    │  TOKENSAVER.md  │
                    │  (universal     │
                    │   first-pass    │
                    │   entry point)  │
                    └────────┬────────┘
                             │
          ┌──────────────────┼──────────────────┐
          │                  │                  │
    ┌─────▼──────┐   ┌──────▼──────┐   ┌───────▼────────┐
    │  CLAUDE.md │   │  AGENTS.md  │   │ .cursor/rules/ │
    │  (adapter) │   │  (adapter)  │   │ (adapter)      │
    └────────────┘   └─────────────┘   └────────────────┘
```

One set of compressed context files. One universal entry point. Optional thin adapters.

---

## Maintenance Flow

Context is updated **incrementally** — never rebuilt from scratch.

```
After a session that:
  Creates new files/dirs     → update codebase-map.md
  Makes an architecture call → add to decisions.md
  Finds a surprise           → add to gotchas.md
  Changes conventions        → update conventions.md
  Leaves work unfinished     → update session-handoff.md
```

## Related

- [[philosophy]] — why TokenSaver exists
- [[best-practices]] — how to use it effectively
