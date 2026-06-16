# Best Practices

> How to get the most from TokenSaver — initialization, daily use, and multi-tool workflows.

---

## Initialization

### When to Initialize

**Good times:**
- After the first working prototype
- Before your first AI coding session on the project
- When a second developer joins

**Too early:**
- Before any code exists
- Mid-rewrite when architecture is fluid

### The Session

```bash
# Claude Code (first-class Skill)
claude
> /tokensaver

# Other tools — run initialization once
# Then any AI coding session reads .tokensaver/ via TOKENSAVER.md
```

TokenSaver will:
1. Read key files (README, configs, entry points — not the whole repo)
2. Create `.tokensaver/` with core context files
3. Create `TOKENSAVER.md` as the universal entry point
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

A 70% complete context used daily beats a 100% complete one that's never written.

---

## Daily Workflow

### Start

No special action needed. AI coding agents automatically load entry points → context files.

### During

Agents reference context as needed — conventions when writing code, architecture when designing changes, gotchas when debugging.

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

Context files live in git. All AI coding tools on the project read the same files → shared understanding across tools and team members.

### Tool-Specific Entry Points

| File | Role | When Created |
|------|------|-------------|
| `TOKENSAVER.md` | Universal entry point | Always |
| `CLAUDE.md` | Thin adapter for Claude Code | Only if already exists or user requests |
| `AGENTS.md` | Thin adapter for Codex, Gemini CLI, etc. | Only if already exists or user requests |
| `.cursor/rules/tokensaver.mdc` | Thin adapter for Cursor | Only if `.cursor/` exists or user requests |

All adapters just point to `TOKENSAVER.md`. All tools ultimately read the same `.tokensaver/` directory.

### Adding a New Tool

If your team adopts a new AI coding tool:
1. Check if it reads `TOKENSAVER.md` — if so, no changes needed
2. If not, check if it has its own instruction file format — add a brief reference to `TOKENSAVER.md`
3. Keep the context files themselves unchanged

---

## Team Usage

### Shared Context

Context files live in git. All team members' AI sessions read the same files → shared understanding.

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

### 5. Skipping Entry Points

**Bad:** Creating `.tokensaver/` but not creating `TOKENSAVER.md`. **Good:** Always create the universal entry point as the final step.

### 6. Over-Engineering the Structure

**Bad:** Creating 10+ files for a 500-line project. **Good:** Start with 3–4 files. Add more only when existing files grow beyond 100 lines.

---

## Summary

| Practice | Good | Bad |
|----------|------|-----|
| **Discovery** | Read high-signal files, stop at ~5K tokens | Scan entire repo |
| **Init** | 3–7 files, usable in 15 min | 10+ files, try to be complete |
| **Updates** | Targeted, 2 min after each session | Rebuild all files quarterly |
| **Detail** | Conceptual models, patterns | Every function, endpoint, prop |
| **Audience** | AI coding context window | Human documentation |
| **Tools** | Agent-agnostic everywhere | Tied to one tool |
