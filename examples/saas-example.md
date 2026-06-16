# AI Agent Framework — TokenSaver Example

> A lightweight agent-agnostic project context system for **AgentOS**, an open-source Python AI agent framework with multi-model support and tool orchestration.

---

## Generated Files

```
.tokensaver/
├── overview.md
├── architecture.md
├── codebase-map.md
├── conventions.md
├── decisions.md
├── gotchas.md
└── session-handoff.md

TOKENSAVER.md
```

---

## .tokensaver/overview.md

```markdown
# AgentOS — Overview

> Open-source Python framework for AI agents. Multi-model, async-first, tool orchestration.

**Last updated:** 2026-05-20

---

## What

Build production AI agents with: multi-model support (Claude, GPT, Gemini, Ollama), first-class tool definitions, sub-agent orchestration, vector memory, and OpenTelemetry observability.

## Tech Stack

| Layer | Tech |
|-------|------|
| Language | Python 3.11+ (async/await) |
| Package mgmt | uv |
| Type checking | mypy (strict) |
| Linting | ruff |
| Testing | pytest + pytest-asyncio |
| Docs | MkDocs Material |
| Distribution | PyPI |

## Key URLs

- Docs: https://agentos.dev
- GitHub: https://github.com/agentos/agentos
- PyPI: https://pypi.org/project/agentos/

## Related
- [[codebase-map]] — directory structure
- [[architecture]] — system design
```

---

## .tokensaver/codebase-map.md

```markdown
# AgentOS — Codebase Map

> Python AI agent framework. Async-first, provider-agnostic.

**Last updated:** 2026-05-20

---

```
agentos/
├── src/agentos/                # Main package
│   ├── agent.py                # Core Agent class + execution loop
│   ├── model/                  # Model provider abstractions
│   │   ├── base.py             # Abstract ModelProvider
│   │   ├── anthropic.py        # Claude (Anthropic API)
│   │   ├── openai.py           # GPT (OpenAI API)
│   │   ├── google.py           # Gemini (Google AI)
│   │   └── ollama.py           # Local models
│   ├── tools/                  # Tool system (Protocol-based)
│   │   ├── base.py             # Tool protocol + @tool decorator
│   │   ├── registry.py         # Namespaced tool registry
│   │   └── builtin/            # Built-in tools (search, code, http)
│   ├── memory/                 # Memory management
│   │   ├── conversation.py     # Sliding window + summarization
│   │   ├── vector_store.py     # Chroma (default) or Pinecone
│   │   └── working_memory.py   # Agent scratchpad
│   ├── orchestration/          # Sub-agent spawning & handoff
│   └── observability/          # OpenTelemetry tracing + structlog
├── tests/
│   ├── unit/                   # Mirrors src/agentos/ structure
│   └── integration/            # VCR.py for model API recording
├── examples/                   # Example agents (simple → complex)
└── pyproject.toml              # Dependencies, mypy config
```

## Key Patterns

- **Abstract base classes for all pluggable components.** ModelProvider, Tool, MemoryStore — everything behind an interface.
- **Async-first.** All IO is async. Sync libraries run in thread pools via `anyio`.
- **Single public API surface.** `from agentos import Agent`. Internal modules prefixed `_`.
- **Pydantic at boundaries.** Config, tool schemas, API responses — all validated.

## Related
- [[conventions]] — Python conventions
- [[architecture]] — component architecture
```

---

## .tokensaver/decisions.md

```markdown
# AgentOS — Decisions

**Last updated:** 2026-05-20

---

## ADR-001: Async-First

**Date:** 2025-06-01 | **Status:** Accepted

**Context:** Agent frameworks need concurrent model calls, tool executions, sub-agent coordination.

**Decision:** All public APIs are async. `anyio` for structured concurrency.

**Rationale:**
- Model APIs are inherently async (HTTP)
- Tool execution involves parallel operations
- `asyncio` is mature and well-understood

**Consequences:** Users write async code. Sync-only libs run in thread pool. Debugging async stack traces requires structlog.

---

## ADR-002: Protocol-Based Tools

**Date:** 2025-07-15 | **Status:** Accepted

**Context:** Users need to define custom tools. Options: ABC, Protocol, decorator.

**Decision:** `typing.Protocol` for interface. `@tool` decorator for simple cases.

**Rationale:**
- No inheritance required — any matching object works
- Users wrap existing code without hierarchy changes
- Pydantic integration cleaner with protocols

**Consequences:** Static-only type checking (mypy). Runtime errors if not type-checked.

---

## ADR-003: Chroma as Default Vector Store

**Date:** 2025-08-01 | **Status:** Accepted

**Context:** Need default vector store for agent memory.

**Decision:** Chroma (embedded) as default. Pinecone for production scale.

**Rationale:**
- Zero config — runs embedded
- No API key or cloud dependency for getting started
- Same MemoryStore interface — one config line to switch

**Consequences:** Performance degrades at >100K embeddings. Two code paths.
```

---

## .tokensaver/gotchas.md

```markdown
# AgentOS — Gotchas

> Surprising behaviors in async Python, model APIs, and tools.

**Last updated:** 2026-05-20

---

## Async

- **Cancellation is cooperative.** `anyio.TaskGroup` cancels only at `await` points. CPU-bound tools must yield periodically.
- **Event loop must be running before Agent creation.** Some HTTP clients bind on import. Always `async with Agent(...)`.

## Model Providers

- **Claude tool calling != OpenAI tool calling.** Claude can return multiple tool calls in one response. Agent loop handles `list[ToolCall]`.
- **Token counts are provider-specific.** Don't compare across providers. Tracked separately in `token_counter.py`.
- **Streaming complicates the agent loop.** Tool calls appear mid-stream. Must buffer chunks.

## Tools

- **Default timeout: 30s.** Override with `timeout_seconds` for long-running tools.
- **Tool names must be unique per namespace.** Registry silently overwrites duplicates.

## Memory

- **Vector search is approximate.** For fact-based queries, use conversation history.
- **Summarization is lossy.** Critical info should go in working memory, not just conversation.

## Related
- [[architecture]] — component interactions
```

---

## .tokensaver/session-handoff.md

```markdown
# AgentOS — Session Handoff

**Last updated:** 2026-05-20

---

## In Progress

- Gemini 2.5 integration: tool calling format adapter built, needs rate-limit testing
- Chroma 1.0 migration: breaking `persist()` API change handled, verifying Windows compatibility

## Next Steps

- Add streaming support for Ollama provider
- Implement agent-to-agent handoff protocol (spec drafted in `docs/rfcs/`)
- Token budget management: add per-session token cap with automatic summarization

## Blockers

- Chroma 1.0 Windows client flaky — investigating with upstream

## Last Session

Added Gemini 2.5 Flash + Pro support. New `src/agentos/model/google.py`. Gemini has different tool calling format — added adapter. Rate limits are restrictive (60 req/min) — documented in gotchas.md.
```

---

## Optional: AGENTS.md Adapter

If the project needs generic agent compatibility, add this thin adapter:

```markdown
# AGENTS.md

## TokenSaver
Read `TOKENSAVER.md` and `.tokensaver/` before scanning source code.
```

---

*This example shows an agent-agnostic context system for an open-source Python framework. The focus is on API design decisions, async patterns, and provider-specific gotchas. `TOKENSAVER.md` is the universal entry point.*
