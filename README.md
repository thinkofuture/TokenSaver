<p align="center">
  <picture>
    <img src="assets/logo.png" alt="TokenSaver Logo" width="96" style="background: #ffffff; padding: 16px; border-radius: 20px;" />
  </picture>
</p>

<h1 align="center">TokenSaver</h1>

<p align="center">
  <strong>Save AI coding tokens. Stop re-reading your repo.</strong><br>
  Compressed first-pass context for AI coding agents.
</p>

<p align="center">
  <a href="#english">English</a> · <a href="#中文">中文</a>
</p>

<p align="center">
  <sub>Agent-agnostic. Claude Code has first-class Skill support. Plain Markdown everywhere.</sub>
</p>

---

<a id="english"></a>

## English

### Why

AI coding agents burn **10,000–20,000 tokens** on every new session just to understand your project — scanning directories, reading configs, and tracing code before writing a single line.

TokenSaver creates a **compressed first-pass context layer**. Agents read `.tokensaver/` first — a short, structured index — instead of scanning the whole repo. They only dive into source files when implementation details are actually needed.

```
Without TokenSaver:  [repo scan: ~15,000 tokens] → [work: ~5,000 tokens]  (every time)
With TokenSaver:     [first-pass: ~1,500 tokens]  → [work: ~5,000 tokens]  (~85% saved)
```

### What It Creates

```
.tokensaver/
├── overview.md              # Project identity and tech stack
├── architecture.md          # System design and data flow
├── codebase-map.md          # Annotated directory tree
├── conventions.md           # Naming, patterns, code style
├── decisions.md             # Architecture decisions & rationale
├── gotchas.md               # Known pitfalls and surprises
└── session-handoff.md       # In-progress work and next steps

TOKENSAVER.md               # Universal entry point — agents read here first
```

Optional adapters (thin pointers for tools that don't natively read `TOKENSAVER.md`):
- `CLAUDE.md` — if Claude Code needs an explicit pointer
- `AGENTS.md` — if Codex / Gemini CLI / Cline need an explicit pointer
- `.cursor/rules/tokensaver.mdc` — if the project uses Cursor with `.cursor/`

> TokenSaver is a compressed first-pass context layer. It replaces expensive full-repo scanning — not an extra layer on top of what already exists.

### For Mature Projects

If your project already has `CLAUDE.md`, `AGENTS.md`, docs, or rules:

- **Already short and clear?** TokenSaver stays minimal — a lightweight index that points agents to existing files in the right order.
- **Too long or scattered?** TokenSaver distills and compresses into `.tokensaver/`, reducing the tokens needed for a first-pass read.

The goal: agents need fewer tokens to get oriented, never more.

| File | Purpose |
|------|---------|
| `overview.md` | Project identity, goals, and tech stack summary |
| `architecture.md` | System design, modules, and data flow |
| `codebase-map.md` | Annotated directory map for fast navigation |
| `conventions.md` | Naming, structure, and coding patterns |
| `decisions.md` | Architectural decisions and rationale |
| `gotchas.md` | Known pitfalls and surprising behaviors |
| `session-handoff.md` | Last session summary, next steps, and blockers |

### Compatibility

TokenSaver is agent-agnostic by design. `TOKENSAVER.md` is the universal entry point. Adapter files are thin pointers for tools that don't natively read `TOKENSAVER.md`.

| Tool | How It Works |
|------|-------------|
| **Claude Code** | First-class `/tokensaver` Skill + reads `TOKENSAVER.md` |
| **Cursor** | `.cursor/rules/tokensaver.mdc` → `TOKENSAVER.md` |
| **Codex / OpenAI** | `AGENTS.md` → `TOKENSAVER.md` |
| **Gemini CLI** | `AGENTS.md` → `TOKENSAVER.md` |
| **Aider** | Reads `TOKENSAVER.md` directly |
| **Windsurf** | `AGENTS.md` → `TOKENSAVER.md` |
| **Cline** | `AGENTS.md` → `TOKENSAVER.md` |
| **Roo Code** | `AGENTS.md` → `TOKENSAVER.md` |

Adapters are created only when the project already uses those files or the user explicitly requests them. They never overwrite existing content. Each adapter block is wrapped in `<!-- tokensaver:start -->` / `<!-- tokensaver:end -->` markers.

### Quick Start

**Claude Code (First-Class Skill)**

```bash
git clone https://github.com/thinkofuture/TokenSaver.git ~/.claude/skills/tokensaver
```

Then in any project:

```
/tokensaver
```

TokenSaver assesses existing configuration, creates `.tokensaver/` and `TOKENSAVER.md`. Adapter files are only created when applicable — always as appended blocks, never overwrites.

**Daily Usage**

```bash
# Start — context loads automatically via TOKENSAVER.md
claude

# End — update only what changed
claude "Update project context with today's changes"
```

### FAQ

<details>
<summary><b>Is TokenSaver Claude Code only?</b></summary>

No. TokenSaver is agent-agnostic. Claude Code has first-class Skill support, but `.tokensaver/` works with any AI coding tool that can read project files — Cursor, Codex, Gemini CLI, Aider, Windsurf, Cline, Roo Code, and more.

</details>

<details>
<summary><b>Does it replace project documentation?</b></summary>

No. TokenSaver is a compressed first-pass context layer for AI agents, not human documentation. Keep your `README.md`, wiki, and API docs. They serve different purposes.

</details>

<details>
<summary><b>Doesn't this add more reading for the agent?</b></summary>

No. TokenSaver replaces expensive full-repo scanning (~15,000 tokens) with a short structured read (~1,500 tokens). The agent reads less total content, not more. For mature projects with existing docs, TokenSaver compresses and indexes what's already there — reducing total first-pass token cost.

</details>

<details>
<summary><b>Should I commit .tokensaver/ to git?</b></summary>

Yes. TokenSaver context files are designed to be version-controlled. They evolve with your code and provide shared understanding for the entire team.

</details>

<details>
<summary><b>What if the context becomes stale?</b></summary>

Update it. TokenSaver is designed for incremental maintenance — 2 minutes after each session updates only the files affected by the change. Run a health check periodically: "Check if the project context is still accurate."

</details>

### Examples

- [Next.js SaaS](examples/nextjs-example.md) — Next.js 14, Prisma, Stripe
- [React Native App](examples/react-native-example.md) — Expo, Supabase, Zustand
- [AI Agent Framework](examples/saas-example.md) — Python, async, multi-model

### Docs

- [Philosophy](docs/philosophy.md)
- [Architecture](docs/memory-system.md)
- [Best Practices](docs/best-practices.md)

### License

MIT © 2026

---

<a id="中文"></a>

## 中文

### 为什么需要 TokenSaver

AI coding agent 的每次新会话都从零开始。为了理解你的项目，它需要扫描目录结构、读取配置文件、追踪代码逻辑——在写第一行代码之前就要消耗 **10,000–20,000 tokens**。

TokenSaver 创建一个**压缩优先读取的上下文层**。Agent 先读 `.tokensaver/`——短小精悍的结构化索引——代替全仓库扫描。只有在实现细节真正需要时，才继续读源文件。

```
没有 TokenSaver：  [仓库扫描: ~15,000 tokens] → [实际工作: ~5,000 tokens]  (重复)
使用 TokenSaver：  [优先读取: ~1,500 tokens]  → [实际工作: ~5,000 tokens]  (节省 ~85%)
```

### 它会生成什么

```
.tokensaver/
├── overview.md              # 项目定位和技术栈
├── architecture.md          # 系统架构和数据流
├── codebase-map.md          # 带注释的目录树
├── conventions.md           # 命名、结构和代码规范
├── decisions.md             # 架构决策及原因
├── gotchas.md               # 已知坑点和容易误判的地方
└── session-handoff.md       # 进行中的工作、下一步和阻塞点

TOKENSAVER.md               # 统一入口 — agent 优先从这里开始
```

可选适配器（thin pointers，引导不原生读取 `TOKENSAVER.md` 的工具）：
- `CLAUDE.md` — 如果 Claude Code 需要显式指针
- `AGENTS.md` — 如果 Codex / Gemini CLI / Cline 需要显式指针
- `.cursor/rules/tokensaver.mdc` — 如果项目使用 Cursor 和 `.cursor/`

> TokenSaver 是压缩优先读取的上下文层。它替代昂贵的全仓库扫描——不是已有配置之上的额外一层。

### 针对成熟项目

如果你的项目已有 `CLAUDE.md`、`AGENTS.md`、docs 或 rules：

- **已经短小清晰？** TokenSaver 保持最小化——轻量索引，按正确顺序指向已有文件。
- **太长或散乱？** TokenSaver 提炼压缩到 `.tokensaver/`，减少优先读取所需的 token 数。

目标：agent 需要更少的 token 来理解项目，而不是更多。

| 文件 | 作用 |
|------|------|
| `overview.md` | 项目定位、目标和技术栈摘要 |
| `architecture.md` | 系统架构、模块关系和数据流 |
| `codebase-map.md` | 带注释的目录地图，帮助快速定位代码 |
| `conventions.md` | 命名、结构和代码模式 |
| `decisions.md` | 架构决策及原因 |
| `gotchas.md` | 已知坑点和容易误判的地方 |
| `session-handoff.md` | 上次会话总结、下一步和阻塞点 |

### 兼容性

TokenSaver 从设计上就是 agent-agnostic 的。`TOKENSAVER.md` 是统一入口。适配器文件是 thin pointers，用于不原生读取 `TOKENSAVER.md` 的工具。

| 工具 | 使用方式 |
|------|---------|
| **Claude Code** | 一等 `/tokensaver` Skill + 读取 `TOKENSAVER.md` |
| **Cursor** | `.cursor/rules/tokensaver.mdc` → `TOKENSAVER.md` |
| **Codex / OpenAI** | `AGENTS.md` → `TOKENSAVER.md` |
| **Gemini CLI** | `AGENTS.md` → `TOKENSAVER.md` |
| **Aider** | 直接读取 `TOKENSAVER.md` |
| **Windsurf** | `AGENTS.md` → `TOKENSAVER.md` |
| **Cline** | `AGENTS.md` → `TOKENSAVER.md` |
| **Roo Code** | `AGENTS.md` → `TOKENSAVER.md` |

适配器仅在项目已有对应文件或用户明确要求时才创建。绝不覆盖已有内容。每个 adapter block 用 `<!-- tokensaver:start -->` / `<!-- tokensaver:end -->` 标记包裹。

### 快速开始

**Claude Code Skill 安装**

```bash
git clone https://github.com/thinkofuture/TokenSaver.git ~/.claude/skills/tokensaver
```

在任意项目中运行：

```
/tokensaver
```

TokenSaver 先评估已有配置，创建 `.tokensaver/` 和 `TOKENSAVER.md`。适配器文件仅在适用时才创建——始终以追加 block 方式，绝不覆盖。

**日常使用**

```bash
# 开始 — TOKENSAVER.md 自动加载上下文
claude

# 结束 — 只更新变化的部分
claude "Update project context with today's changes"
```

### 常见问题

<details>
<summary><b>TokenSaver 只支持 Claude Code 吗？</b></summary>

不是。TokenSaver 是 agent-agnostic 的。Claude Code 有一等 Skill 支持，但 `.tokensaver/` 可以用于任何能读取项目文件的 AI coding 工具——Cursor、Codex、Gemini CLI、Aider、Windsurf、Cline、Roo Code 等。

</details>

<details>
<summary><b>它会替代项目文档吗？</b></summary>

不会。TokenSaver 是给 AI agent 用的压缩优先读取上下文层，不是给人看的文档。保留你的 `README.md`、wiki 和 API docs——它们服务于不同的目的。

</details>

<details>
<summary><b>这不是让 agent 读更多文件吗？</b></summary>

正相反。TokenSaver 用短小精悍的结构化读取（~1,500 tokens）替代昂贵的全仓库扫描（~15,000 tokens）。Agent 读取的总量更少，不是更多。对于已有配置的成熟项目，TokenSaver 是压缩索引——减少优先读取的 token 成本。

</details>

<details>
<summary><b>.tokensaver/ 要提交到仓库吗？</b></summary>

要。TokenSaver 上下文文件就是设计用来版本控制的。它们随代码一起演进，为整个团队提供共享的项目理解。

</details>

<details>
<summary><b>如果上下文过期了怎么办？</b></summary>

更新它。TokenSaver 设计为增量维护——每次会话结束后花 2 分钟更新受影响的文件。定期运行健康检查："Check if the project context is still accurate."

</details>

### 示例

- [Next.js SaaS](examples/nextjs-example.md) — Next.js 14, Prisma, Stripe
- [React Native App](examples/react-native-example.md) — Expo, Supabase, Zustand
- [AI Agent Framework](examples/saas-example.md) — Python, async, multi-model

### 文档

- [设计哲学](docs/philosophy.md)
- [系统架构](docs/memory-system.md)
- [最佳实践](docs/best-practices.md)

### License

MIT © 2026
