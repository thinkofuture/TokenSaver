# Best Practices

> How to get the most from TokenSaver — initialization, daily use, and multi-tool workflows.

---

## Initialization

### When to Initialize

**Good times:**
- After the first working prototype
- Before your first AI coding session on the project
- When a second developer joins
- When existing project docs have grown too long or scattered

**Too early:**
- Before any code exists
- Mid-rewrite when architecture is fluid

### Assess Before Creating

Before creating any files, check what already exists:

1. Look for existing `CLAUDE.md`, `AGENTS.md`, `.cursor/rules/`, `README.md`
2. Are they already short, clear, and well-structured?
3. **If yes** — TokenSaver creates a minimal `TOKENSAVER.md` index that points agents to existing files in the right read order
4. **If no** (too long, scattered, redundant) — TokenSaver distills and compresses into `.tokensaver/`

The goal: agents need fewer total tokens for first-pass context, never more.

### The Session

```bash
# Claude Code (first-class Skill)
claude
> /tokensaver

# Other tools — run initialization once
# Then any AI coding session reads .tokensaver/ via TOKENSAVER.md
```

TokenSaver will:
1. Assess existing configuration files
2. Create `.tokensaver/` with core context files (compressing if needed)
3. Create `TOKENSAVER.md` as the universal first-pass entry point
4. If applicable, append brief adapter blocks to existing `CLAUDE.md`, `AGENTS.md`, or `.cursor/rules/`

```bash
# Review and commit
git add .tokensaver/ TOKENSAVER.md
git commit -m "Initialize project context with TokenSaver"
```

### What to Cover First

1. **Codebase map** — Where things live (most-referenced file)
2. **Conventions** — How code is written (most expensive to re-learn)
3. **Gotchas** — What will waste time if unknown
4. **Architecture** — Hardest to rediscover

A 70% complete compressed context used daily beats a 100% complete one that's never written.

---

## Daily Workflow

### Start

No special action needed. AI coding agents automatically load entry points → first-pass context.

### During

Agents read on-demand context as needed — conventions when writing code, architecture when designing changes, gotchas when debugging. Source files are only read when implementation details are required.

### End — The 2-Minute Update

After each significant session, update **only what changed:**

```bash
# Claude Code
claude "Update project context with today's changes"

# Any tool — edit the relevant files directly
```

| What Happened | Update |
|--------------|--------|
| New dirs/files created | `codebase-map.md` |
| Architecture decision | `decisions.md` |
| Surprising behavior | `gotchas.md` |
| Convention change | `conventions.md` |
| Unfinished work | `session-handoff.md` |

---

## Multi-Tool Usage

### Shared Context

Context files live in git. All AI coding tools on the project read the same compressed first-pass → shared understanding across tools and team members.

### Tool-Specific Entry Points

| File | Role | When Created |
|------|------|-------------|
| `TOKENSAVER.md` | Universal first-pass entry point | Always |
| `CLAUDE.md` | Thin adapter for Claude Code | Only if already exists or user requests |
| `AGENTS.md` | Thin adapter for Codex, Gemini CLI, etc. | Only if already exists or user requests |
| `.cursor/rules/tokensaver.mdc` | Thin adapter for Cursor | Only if `.cursor/` exists or user requests |

All adapters just point to `TOKENSAVER.md` via `<!-- tokensaver:start -->` / `<!-- tokensaver:end -->` blocks. All tools ultimately read the same `.tokensaver/` directory.

### Adding a New Tool

If your team adopts a new AI coding tool:
1. Check if it reads `TOKENSAVER.md` — if so, no changes needed
2. If not, check if it has its own instruction file format — add a brief adapter pointing to `TOKENSAVER.md`
3. Keep the context files themselves unchanged

---

## Team Usage

### Shared Context

Context files live in git. All team members' AI sessions read the same compressed context → shared understanding.

### PR Reviews

If a PR changes architecture or patterns, reviewers should check whether context needs updating.

### Disagreements

When the team disagrees on conventions: document the decision in `decisions.md`, not the debate.

---

## Common Mistakes

### 1. Full Repo Scan for Discovery

**Bad:** Reading every file to create "complete" context. **Good:** Reading high-signal files only. Fill gaps with `TODO:` markers.

### 2. Over-Detailed Context

**Bad:** Listing every endpoint, every component prop. **Good:** Capturing the conceptual model and patterns. Agents can grep for specifics.

### 3. Write Once, Never Update

**Bad:** Creating context at initialization and never touching it. **Good:** 2-minute end-of-session updates.

### 4. Rebuilding Instead of Incrementally Updating

**Bad:** Re-running full discovery and regenerating all files. **Good:** Targeted updates to only the files affected by the change.

### 5. Skipping TOKENSAVER.md

**Bad:** Creating `.tokensaver/` but not creating `TOKENSAVER.md`. **Good:** Always create the universal entry point as the final step.

### 6. Piling On Without Compressing

**Bad:** Adding `.tokensaver/` on top of long existing docs without compressing — making agents read *more*, not less. **Good:** If existing config is already clear, index it. If it's too long, distill it. TokenSaver always reduces total first-pass tokens.

### 7. Over-Engineering the Structure

**Bad:** Creating 10+ files for a 500-line project. **Good:** Start with 3–4 files. Add more only when existing files grow beyond 100 lines.

---

## Summary

| Practice | Good | Bad |
|----------|------|-----|
| **Discovery** | Read high-signal files, stop at ~5K tokens | Scan entire repo |
| **Init** | 3–7 files, usable in 15 min | 10+ files, try to be complete |
| **Updates** | Targeted, 2 min after each session | Rebuild all files quarterly |
| **Detail** | Conceptual models, patterns | Every function, endpoint, prop |
| **Token impact** | Fewer total tokens for first-pass | More reading burden |
| **Audience** | AI coding context window | Human documentation |
| **Tools** | Agent-agnostic everywhere | Tied to one tool |
