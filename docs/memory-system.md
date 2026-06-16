# Context System Architecture

> How TokenSaver works: file structure, linking, multi-tool entry points, and context management.

---

## Three Layers

```
Layer 1: TOKENSAVER.md         ← Universal entry point (read every session)
    │
    ▼
Layer 2: Context Files         ← Structured knowledge in .tokensaver/
    │
    ▼
Layer 3: Actual Code           ← The repository (source of truth)
```

---

## Layer 1: Entry Points

### TOKENSAVER.md (Core Entry Point — always created)

`TOKENSAVER.md` is the single universal entry point for TokenSaver. All AI agents start here. It tells agents what `.tokensaver/` contains and in what order to read it. Target 5–15 lines.

```markdown
# TOKENSAVER.md

## TokenSaver Project Context

Before scanning the repository, read the TokenSaver context files first.
Start with:
1. `.tokensaver/session-handoff.md`
2. `.tokensaver/overview.md`
3. `.tokensaver/codebase-map.md`

Use `.tokensaver/architecture.md`, `.tokensaver/conventions.md`, `.tokensaver/decisions.md`, and `.tokensaver/gotchas.md` when relevant.
Treat source code as the source of truth. TokenSaver files are a compressed guide, not a replacement for code.
Only inspect source files when implementation details are needed.
```

### Adapter Files (Optional — thin wrappers)

Adapters exist solely to guide tools that don't natively read `TOKENSAVER.md`. They are **not created by default**. Each is just a brief pointer:

**CLAUDE.md** — only if the project already has one or the user asks:

```markdown
## TokenSaver
Read `TOKENSAVER.md` and `.tokensaver/` before scanning source code.
```

**AGENTS.md** — only if the project already has one or the user asks:

```markdown
## TokenSaver
Read `TOKENSAVER.md` and `.tokensaver/` before scanning source code.
```

**Cursor rules** — only if `.cursor/` exists or the user asks:

```markdown
---
description: Use TokenSaver project context before scanning the repo
alwaysApply: true
---
Read `TOKENSAVER.md` and `.tokensaver/` before scanning source code.
```

**Critical:** Adapters never overwrite existing content. If a file already has content, the TokenSaver block is appended at the end.

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

Context is a map, not the territory. AI agents still read source files. Context reduces the search space from "the entire repo" to "the relevant files."

---

## Context Window Management

### Typical Token Budget

For a medium project:

| What | Tokens |
|------|--------|
| Entry point (TOKENSAVER.md) | ~150 |
| overview.md | ~300 |
| codebase-map.md | ~400 |
| session-handoff.md | ~200 |
| **Base load (every session)** | **~1,050** |
| conventions.md (when writing code) | ~400 |
| architecture.md (when designing) | ~500 |
| **Peak (relevant files loaded)** | **~2,000** |

Compare to full repo scan: **10,000–20,000 tokens**. ~85–90% reduction.

### Progressive Loading

```
Session Start (always):
  ├── TOKENSAVER.md
  ├── overview.md
  ├── codebase-map.md
  └── session-handoff.md

During Session (on-demand):
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
                    │  (source of      │
                    │   context truth) │
                    └────────┬────────┘
                             │
                    ┌────────▼────────┐
                    │  TOKENSAVER.md  │
                    │  (universal     │
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

One set of context files. Multiple entry points for different AI coding tools.

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
