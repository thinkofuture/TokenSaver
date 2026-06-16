# Philosophy

> Why TokenSaver exists and the principles behind its design.

---

## The Problem

AI coding agents start every session with zero knowledge. They must scan directories, read config files, and trace through code to understand a project. This burns **10,000–20,000 tokens** before they write a single line.

This cost is paid *every session*. Over hundreds of sessions, millions of tokens are wasted on redundant repository discovery. This problem is not specific to any one AI coding tool — it affects Claude Code, Cursor, Codex, Gemini CLI, Aider, Windsurf, Cline, and every other agent that starts from scratch.

## The Two Failed Approaches

**Trap: Auto-generation.** Tools that scan the entire repo and generate documentation. This defeats the purpose — you're still scanning. The output is bloated and rarely maintained.

**Trap: Human documentation.** Comprehensive architecture docs that take weeks to write and are stale by completion. The maintenance burden exceeds the perceived value.

## The TokenSaver Approach

**Generated once, then curated over time.**

- The initial context is drafted from a **targeted reading** of high-signal files — not a full repo scan.
- After that, it's updated **incrementally**. Each change touches only the relevant files.
- Project context is the **compressed index and guide** to the codebase, not a replacement for the code.
- **Agent-agnostic by design.** Works with any AI tool that reads Markdown files.

---

## Design Principles

### 1. Just-in-Time, Not Just-in-Case

Context captures what AI coding agents need to be productive. If an agent can discover something with a quick grep, it doesn't belong in the context files.

**Good:** Architecture patterns, naming conventions, known pitfalls, decision rationale.
**Bad:** Every function signature, every API parameter, every file path.

### 2. Signal Density Over Completeness

A 100-line context file read every session beats a 1,000-line file that's never opened. Every sentence must earn its place in the context window.

### 3. Incremental, Not Rebuilt

Context degrades when not maintained — but maintenance must be lightweight. End-of-session updates target only what changed. Never rebuild all files from scratch.

### 4. Code is the Source of Truth

When context and code disagree, code wins. Context documents intent and rationale; code is what actually runs.

### 5. Version-Controlled Knowledge

Context lives in git alongside code. It evolves with PRs and is reviewed like any other change. This ensures team-shared understanding and historical context.

### 6. Agent-Agnostic by Default

Plain Markdown with `[[wikilinks]]`. Any AI coding tool can read it. No schema, no dependencies, no lock-in. Claude Code gets first-class Skill support; every other tool uses the same files through their own mechanisms.

---

## What TokenSaver Is Not

### Not Documentation

TokenSaver serves AI coding agents, not humans. Keep your `README.md`, `CONTRIBUTING.md`, and API docs — they serve different purposes.

### Not a Replacement for Reading Code

Context is a map, not the territory. AI agents still read source files. Context tells them *where to look* and *what patterns to expect*.

### Not Auto-Generated

No script scans your repo. The curation is the value. A human (or an AI agent, guided by a human) decides what's important.

### Not Claude Code Only

While Claude Code has first-class Skill support, the `.tokensaver/` directory is a tool-agnostic standard. Any AI coding tool that reads project files can use it.

---

## Why This Works

AI coding agents need three things to be productive:

1. **Orientation:** What is this project? Where do things live?
2. **Constraints:** What patterns should I follow? What should I avoid?
3. **Context:** Why were decisions made? What surprises await?

TokenSaver provides exactly this — and nothing more. For any agent.

---

## Related

- [[memory-system]] — technical architecture
- [[best-practices]] — how to use it effectively
- [SKILL.md](../SKILL.md) — the TokenSaver skill definition
