---
name: tokensaver
description: Save AI coding tokens with lightweight, persistent project context. Use when the user wants to reduce repeated repository scans, initialize project context memory, maintain repo knowledge across sessions, or lower AI coding token overhead. Works with any AI coding tool — Claude Code has first-class Skill support.
---

# TokenSaver — Compressed First-Pass Context for AI Coding Agents

TokenSaver creates a **compressed first-pass context layer** for AI coding agents. Instead of scanning an entire repository, agents read a small set of structured Markdown files in `.tokensaver/` that act as a compressed index. This replaces expensive full-repo scanning with a fast, targeted first read — saving thousands of tokens per session.

TokenSaver is **agent-agnostic by design**. It works with any AI coding tool that can read project files. Claude Code has first-class Skill support via the `/tokensaver` command.

## Core Philosophy

AI coding agents are stateless. Every new session starts cold — the agent must re-discover the repo. That burns tokens.

TokenSaver replaces repeated scanning with a **compressed first-pass context layer**. The key insight:

> **Generated once, then curated over time.**

The initial context is drafted from a targeted reading of high-signal files. After that, it's updated **incrementally** — never regenerated from scratch.

TokenSaver is **not an additional layer on top of existing configuration**. It is a **compressed first-pass read** — shorter and more structured than scanning raw project files. Agents only dive into detailed docs, large config files, or source code when implementation details are actually needed.

### Handling Mature Projects

For projects that already have `CLAUDE.md`, `AGENTS.md`, docs, rules, or README:

- **If existing config is already short, clear, and effective** — TokenSaver stays minimal. It creates a lightweight `TOKENSAVER.md` index and a focused `.tokensaver/` that points agents to the right files in the right order. No duplication.
- **If existing config is long, scattered, or redundant** — TokenSaver distills it into `.tokensaver/` and `TOKENSAVER.md`, compressing and indexing what the agent actually needs for a first pass. Source docs remain the territory; TokenSaver is the map.

Principles:
1. **Compressed first-pass, not an extra layer.** TokenSaver replaces full scanning with a structured short read. It does not add context — it replaces expensive discovery with cheap context.
2. **Agent-agnostic.** Context files work with Claude Code, Cursor, Codex, Gemini CLI, Aider, Windsurf, Cline, Roo Code, and any AI tool that reads files.
3. **Single source of truth per concept.** Each fact lives in one file. `[[wikilinks]]` connect related concepts.
4. **Just enough, not exhaustive.** Capture patterns, decisions, and gotchas — not every function signature.
5. **Code is the source of truth.** When context and code disagree, code wins. Update context.
6. **Version-controlled.** Context files live in the repo. They evolve with the code.

## Token Budget Rules

**You are token-constrained. Treat discovery as expensive.**

- **Do NOT read the entire repository by default.** Stop when you have enough to write useful context.
- **Read TokenSaver context files before scanning source code.** Existing `.tokensaver/` files replace the need for broad scanning.
- **Prefer high-signal files first, in this order:**
  1. README (if exists)
  2. Package/config files (`package.json`, `Cargo.toml`, `pyproject.toml`, etc.)
  3. Entry points (`main.ts`, `index.ts`, `app/`, `src/`)
  4. Routing files (Next.js `app/`, React Router config, etc.)
  5. Schema/migration files (Prisma, Django models, etc.)
  6. Existing docs (`CONTRIBUTING.md`, `ARCHITECTURE.md`, `TOKENSAVER.md`, `AGENTS.md`, `CLAUDE.md`)
- **Use directory listing + targeted reads, not recursive grep.** List first, then read only the directories that define architecture.
- **Stop discovery once you can write useful context.** 80% knowledge now > 100% knowledge after scanning 20,000 tokens.
- **If information is uncertain, write `TODO:` markers** instead of reading more files. The next maintenance pass fills them in.
- **Keep context files short and focused.** Target 30–80 lines per file. Max 150 lines.
- **Treat source code as truth.** TokenSaver is a compressed guide, not a replacement for code.
- **Default budget for discovery:** ~2000-5000 tokens of reading. For large repos, cap at 8000 tokens.

## When to Invoke This Skill

**Invoke when:**
- User asks to "set up project context", "initialize AI memory", or mentions TokenSaver / tokensaver
- A project has no `.tokensaver/` yet
- The codebase has changed significantly and context needs targeted updates
- TOKENSAVER.md references the project context directory but it doesn't exist

**Do NOT invoke for:**
- Simple one-off questions
- Searching for code (use grep/Glob)
- Tasks that don't create or modify context files

## Workflow: Initialize Project Context

### Phase 1: Assess Existing Configuration

Before creating anything, check what already exists:

1. Check for existing `CLAUDE.md`, `AGENTS.md`, `.cursor/rules/`, `README.md`, `CONTRIBUTING.md`
2. Evaluate: are these already short, clear, and well-structured?
3. If yes — TokenSaver should be minimal. Create a lightweight index that points to what's already good.
4. If no (too long, scattered, redundant) — TokenSaver should distill and compress.

**For mature projects:** The goal is compression and indexing, not adding more reading. End the session with fewer total tokens needed for first-pass context — never more.

### Phase 2: Discover (Token-Efficient)

**Goal: Learn enough to write useful compressed context. Not a full audit.**

1. Start with a top-level directory listing
2. Read README, package config, and entry points
3. Read existing instruction files (CLAUDE.md, AGENTS.md, .cursor/rules/) — but only to assess whether they need compression
4. Read the routing/layout layer (tells you the module structure)
5. Sample key directories — don't exhaustively list
6. **Stop when you can describe the architecture.**

**Monorepo handling:** Identify packages/workspaces. Create a top-level context file mapping package boundaries. Focus initial context on the package(s) the developer works in most.

**Critical rule: If information is missing, write `TODO:`.** Don't block initialization to ask questions. Default to completing the first pass automatically. Only ask the developer when something is genuinely ambiguous (e.g., "is this a library or an application?").

### Phase 3: Structure

Create `.tokensaver/`. The **default structure** is these 7 files:

```
.tokensaver/
├── overview.md              # Project identity, purpose, tech stack summary
├── architecture.md          # System design, components, data flow
├── codebase-map.md          # Annotated directory tree — what lives where
├── conventions.md           # Code style, naming, patterns, commit conventions
├── decisions.md             # Key architectural decisions and rationale
├── gotchas.md               # Surprising behaviors, known issues, pitfalls
└── session-handoff.md       # What's in progress, what's next, unfinished work
```

**Small projects (<50 files) need fewer files.** Default to: `overview.md`, `codebase-map.md`, `conventions.md`. Add others only when the relevant domain is complex enough to justify a separate file.

**Only create a file when its domain is sufficiently complex.** Don't create empty shells. Don't scan the entire repo to fill a file that doesn't have real content.

**Additional files (only when justified):**
- `data-models.md` — only if the data layer is complex (10+ models, non-obvious relationships)
- `api-contracts.md` — only if there's a public API surface
- `dependencies.md` — only if dependency choices are non-obvious
- `testing-strategy.md` — only if test infrastructure is complex
- `deployment.md` — only if deployment is complex or non-standard
- `state-management.md` — only if state management is complex (e.g., Redux, MobX)
- `migrations.md` — only if there's a non-trivial migration strategy

### Phase 4: Write

Write each file. Follow the format and keep it concise.

#### File Format

```markdown
# [Title]

> [One-sentence summary — what this covers, when to read it]

**Last updated:** YYYY-MM-DD

---

[Scannable content — tables, lists, concise paragraphs]

## Related
- [[other-file]] — brief reason for relationship
```

#### Content for Each Core File

**overview.md** — ~20-40 lines. Project name, one-paragraph description, tech stack (compact table), key URLs. Don't include team org charts or long feature lists.

**architecture.md** — ~30-80 lines. System architecture diagram (ASCII preferred — Mermaid is fine but costs tokens), major components and responsibilities, data flow, deployment topology. Focus on *why*, not just *what*.

**codebase-map.md** — ~30-60 lines. Annotated directory tree. For each directory: one line explaining what it contains and what belongs there. Mark deprecated directories. This is the most-referenced file.

**conventions.md** — ~20-50 lines. Naming, file organization, code style, commit conventions. Focus on what would surprise someone new.

**decisions.md** — One ADR per section. Format: Date, Decision, Context (1-2 sentences), Rationale (bullet list), Consequences. Only record decisions with non-obvious rationale.

**gotchas.md** — Bullet-pointed list. Each: the surprise, the context, the workaround. "X looks like Y but is actually Z because..."

**session-handoff.md** — ~10-30 lines. What's currently in progress, known unfinished work, next steps, any blockers. Updated at end of each significant session. This is the "breadcrumb trail" for the next AI coding session.

#### Writing Style

- Write for AI coding agents, not humans. Be direct, factual, scannable.
- Use `[[wikilinks]]` to connect related files: `[[architecture]]`, `[[gotchas]]`
- Use `code` spans for paths, function names, technical terms.
- Prefer tables for comparison, lists for sequences.
- Keep each file under 150 lines. If growing past that, consider splitting.

### Phase 5: Create Entry Points

TokenSaver has a layered entry-point strategy:

- **`.tokensaver/`** — the compressed context (always created)
- **`TOKENSAVER.md`** — the universal entry point (always created)
- **Adapter files** — thin pointers for tools that don't natively read `TOKENSAVER.md`

Adapter files (`CLAUDE.md`, `AGENTS.md`, `.cursor/rules/tokensaver.mdc`) are **not created by default**. They are only created or updated when the project already uses those files, or the user explicitly requests them.

**Never overwrite existing content.** If an adapter file already exists, only append or update the TokenSaver block. Leave all other content untouched.

#### TOKENSAVER.md (always create)

TOKENSAVER.md is the universal entry point. It tells AI agents that `.tokensaver/` is the compressed first-pass read — they should read it before scanning source code. **Target 5–15 lines.** It is an index, not a duplicate.

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

#### Adapter Files (only when applicable)

Adapter files are thin wrappers. They exist solely to guide tools that don't natively read `TOKENSAVER.md`. They should be minimal — pointing to `TOKENSAVER.md`, not duplicating it.

**CLAUDE.md** — create/update only if:
- The project already has a `CLAUDE.md` (append the TokenSaver block)
- The user explicitly asks for Claude Code integration
- Otherwise, skip it. Claude Code reads `TOKENSAVER.md` natively.

When you do create or update CLAUDE.md, append only:

```markdown
<!-- tokensaver:start -->
## TokenSaver
Read `TOKENSAVER.md` before scanning source code.
<!-- tokensaver:end -->
```

**AGENTS.md** — create/update only if:
- The project already has an `AGENTS.md` (append the TokenSaver block)
- The user asks for Codex / generic agent compatibility
- Otherwise, skip it.

When you do create or update AGENTS.md, append only:

```markdown
<!-- tokensaver:start -->
## TokenSaver
Read `TOKENSAVER.md` before scanning source code.
<!-- tokensaver:end -->
```

**.cursor/rules/tokensaver.mdc** — create/update only if:
- The project already has a `.cursor/` directory
- The user explicitly asks for Cursor support
- Otherwise, skip it.

When you do create it:

```markdown
---
description: Use TokenSaver compressed first-pass context
alwaysApply: true
---
<!-- tokensaver:start -->
Read `TOKENSAVER.md` before scanning source code.
<!-- tokensaver:end -->
```

**Critical: Never overwrite an existing file.** If `CLAUDE.md`, `AGENTS.md`, or a Cursor rule already exists with other content, append only the TokenSaver block between `<!-- tokensaver:start -->` and `<!-- tokensaver:end -->` markers. Do not replace the file. TokenSaver is a guest in the user's project.

## Workflow: Maintain Project Context

**Context is updated incrementally. Never rebuild all files from scratch.**

After a significant coding session, update only the files affected by the change:

| What Changed | Files to Update |
|-------------|----------------|
| New directories or files created | `codebase-map.md` |
| Architectural decision made | `decisions.md` |
| Surprising behavior discovered | `gotchas.md` |
| Convention or pattern shift | `conventions.md` |
| System design changed | `architecture.md` |
| Unfinished work, next steps | `session-handoff.md` |

**Maintenance rules:**
- **Code is the source of truth.** If context conflicts with code, update context.
- **Don't rebuild.** Targeted updates only. Don't re-read the entire repo.
- **Incremental.** If you add a new module, add one section to `codebase-map.md` — don't rewrite the file.
- **Session handoff.** End each session by updating `session-handoff.md` with what's in progress.

**Maintenance prompt (lightweight):**
> "Before we wrap up — I'll update the relevant TokenSaver files with what changed."

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Context files are stale | Run a health check: verify `codebase-map.md` against actual directory structure |
| Context files are too long | Split by topic; keep each file under 150 lines |
| Context contradicts code | Code is truth. Update context. |
| Entry point not reading context | Ensure TOKENSAVER.md explicitly references the files with relative paths |
| TODO markers piling up | Dedicate one session to filling in accumulated TODOs |

## Related Skills
- `init` — Initialize a new TOKENSAVER.md for a project
- `claude-api` — Reference for Claude API capabilities
