<p align="center">
  <img src="assets/logo.png?v=2" alt="TokenSaver Logo" width="120" />
</p>


<h1 align="center">TokenSaver</h1>

<p align="center">
  <strong>Stop paying your AI to reread your repo.</strong><br>
  TokenSaver gives it a small cheat sheet instead.
</p>

<p align="center">
  Turn repeated repo scanning into reusable project context.
</p>


<p align="center">
  <a href="#english">English</a> · <a href="#中文">中文</a>
</p>


---

<a id="english"></a>

## English


### Why TokenSaver?

Your AI agent has no project memory.

Every new session, it opens the same files again: `package.json`, routes, configs, docs, random files under `src/`...

That's fine once.<br>
It's expensive forever.

TokenSaver reads the important bits once, then hands your agent a short cheat sheet it can reuse.

```
Without → agent scans the repo blind:     ~15,000 tokens (every time)
With    → agent reads the cheat sheet:      ~1,500 tokens (once)
```


### What it does

TokenSaver reads your project's key files, then creates:

- **`TOKENSAVER.md`** — a short entry point your agent reads first
- **`.tokensaver/`** — a focused context folder with 7 lightweight Markdown files

Your agent gets oriented in ~1,500 tokens instead of scanning the whole repo. It only opens source files when it actually needs the details.

> TokenSaver replaces expensive full-repo scanning. It's not another layer on top — it's a shortcut.


### What it creates

<details>
<summary><b>.tokensaver/</b> — 7 lightweight Markdown files</summary>

| File | What it tells the agent |
|------|------------------------|
| `overview.md` | What this project is, what it's built with |
| `architecture.md` | How the pieces fit together |
| `codebase-map.md` | Annotated directory tree — where everything lives |
| `conventions.md` | Naming, patterns, code style |
| `decisions.md` | Key architecture decisions and why |
| `gotchas.md` | Known pitfalls — don't step on these |
| `session-handoff.md` | What's in progress, what's next, what's blocked |

</details>


**`TOKENSAVER.md`** — the universal entry point. Every AI tool reads here first.


**Optional thin adapters** — only created when needed, never overwrite existing config:

| Adapter | For |
|---------|-----|
| `CLAUDE.md` | Claude Code auto-load |
| `AGENTS.md` | Codex, Gemini CLI, Cline, Windsurf, Roo Code |
| `.cursor/rules/tokensaver.mdc` | Cursor |

All adapters are thin pointers to `TOKENSAVER.md`. Changes are wrapped in `<!-- tokensaver:start -->` / `<!-- tokensaver:end -->` — your existing config stays untouched.


### How it works with your tools

TokenSaver is **agent-agnostic**. `TOKENSAVER.md` is a plain Markdown file — any AI tool that reads project files can use it.

<details>
<summary><b>Tool compatibility</b></summary>

| Tool | How it loads TokenSaver |
|------|------------------------|
| **Claude Code** | First-class `/tokensaver` Skill + reads `TOKENSAVER.md` |
| **Cursor** | `.cursor/rules/tokensaver.mdc` → `TOKENSAVER.md` |
| **Codex / OpenAI** | `AGENTS.md` → `TOKENSAVER.md` |
| **Gemini CLI** | `AGENTS.md` → `TOKENSAVER.md` |
| **Aider** | Reads `TOKENSAVER.md` directly |
| **Windsurf** | `AGENTS.md` → `TOKENSAVER.md` |
| **Cline** | `AGENTS.md` → `TOKENSAVER.md` |
| **Roo Code** | `AGENTS.md` → `TOKENSAVER.md` |

</details>


### Quick Start

<details>
<summary><b>Claude Code (one-liner)</b></summary>

```bash
git clone https://github.com/thinkofuture/TokenSaver.git ~/.claude/skills/tokensaver
```

Then in any project:

```
/tokensaver
```

TokenSaver scans your project, creates `.tokensaver/` and `TOKENSAVER.md`. Adapter files only appear when needed — as appended blocks, never overwrites.

</details>


<details>
<summary><b>Other tools</b></summary>

Drop this in your first prompt:

> Read `TOKENSAVER.md` and `.tokensaver/` to understand this project before writing any code.

That's it. Your agent now starts with context instead of a blank slate.

</details>


**Daily use:**

```bash
# Start work — context loads automatically via TOKENSAVER.md
claude

# End of session — refresh what changed
claude "Update project context with today's changes"
```


### FAQ

<details>
<summary><b>Is this Claude Code only?</b></summary>

No. Claude Code gets a first-class Skill, but `.tokensaver/` is plain Markdown. Cursor, Codex, Gemini CLI, Aider, Windsurf, Cline, Roo Code — they all read Markdown just fine.

</details>


<details>
<summary><b>Does it replace my README or docs?</b></summary>

No. TokenSaver is for AI agents, not humans. Keep your README, wiki, and API docs — they serve a different audience.

</details>


<details>
<summary><b>Won't this make the agent read more files?</b></summary>

Opposite. It's ~1,500 tokens of structured context instead of ~15,000 tokens of repo scanning. Less reading, faster starts.

</details>


<details>
<summary><b>Should I commit .tokensaver/?</b></summary>

Yes. It's designed to be versioned alongside your code — the whole team's agents benefit from it.

</details>


<details>
<summary><b>What if it goes stale?</b></summary>

Refresh it. Two minutes after a session updates the changed files. Or ask your agent: "Check if the project context is still accurate."

</details>


### Examples & Docs

- Examples: [Next.js SaaS](examples/nextjs-example.md) · [React Native](examples/react-native-example.md) · [AI Agent Framework](examples/saas-example.md)
- Docs: [Philosophy](docs/philosophy.md) · [Architecture](docs/memory-system.md) · [Best Practices](docs/best-practices.md)


### License

MIT © 2026


---

<a id="中文"></a>

## 中文


### 为什么需要 TokenSaver？

别再花 token 让 AI 重读你的仓库。<br>
TokenSaver 把它压缩成一份小抄。

每次新会话，AI 都从零开始翻你的项目：`package.json`、路由、配置、docs、`src/` 下面一堆文件……

第一次还行。<br>
每次都这样，又慢又烧钱。

```
没有 TokenSaver：  AI 盲目扫描仓库:     ~15,000 tokens（每次都这样）
使用 TokenSaver：  AI 先读小抄:          ~1,500 tokens（一次搞定）
```


### 它做什么？

TokenSaver 先读项目关键文件，然后生成：

- **`TOKENSAVER.md`** — 一个简短入口，AI 优先读这里
- **`.tokensaver/`** — 一个轻量上下文目录，7 个 Markdown 文件

AI 用 ~1,500 tokens 就能了解项目全貌，不需要到处翻文件。只有在真正需要实现细节时，才打开源码。

> TokenSaver 替代昂贵的全仓库扫描，不是在已有配置上额外加一层——它是捷径。


### 它会生成什么？

<details>
<summary><b>.tokensaver/</b> — 7 个轻量 Markdown 文件</summary>

| 文件 | 告诉 AI 什么 |
|------|-------------|
| `overview.md` | 项目是什么，用什么技术栈 |
| `architecture.md` | 系统怎么组合在一起 |
| `codebase-map.md` | 带注释的目录树——东西都在哪 |
| `conventions.md` | 命名、结构、代码风格 |
| `decisions.md` | 关键架构决策及原因 |
| `gotchas.md` | 已知坑点——别踩这些 |
| `session-handoff.md` | 进行中的工作、下一步、阻塞点 |

</details>


**`TOKENSAVER.md`** — 统一入口，所有 AI 工具都从这里开始。


**可选适配器** — 只在需要时创建，绝不覆盖已有配置：

| 适配器 | 适用工具 |
|--------|---------|
| `CLAUDE.md` | Claude Code 自动加载 |
| `AGENTS.md` | Codex、Gemini CLI、Cline、Windsurf、Roo Code |
| `.cursor/rules/tokensaver.mdc` | Cursor |

所有适配器都是指向 `TOKENSAVER.md` 的薄指针。修改用 `<!-- tokensaver:start -->` / `<!-- tokensaver:end -->` 包裹——你已有的配置不动分毫。


### 和你的工具怎么配合？

TokenSaver 从设计上就是**工具无关**的。`TOKENSAVER.md` 就是普通 Markdown——任何能读项目文件的 AI 工具都能用。

<details>
<summary><b>工具兼容性</b></summary>

| 工具 | 如何加载 TokenSaver |
|------|-------------------|
| **Claude Code** | 一等 `/tokensaver` Skill + 读取 `TOKENSAVER.md` |
| **Cursor** | `.cursor/rules/tokensaver.mdc` → `TOKENSAVER.md` |
| **Codex / OpenAI** | `AGENTS.md` → `TOKENSAVER.md` |
| **Gemini CLI** | `AGENTS.md` → `TOKENSAVER.md` |
| **Aider** | 直接读取 `TOKENSAVER.md` |
| **Windsurf** | `AGENTS.md` → `TOKENSAVER.md` |
| **Cline** | `AGENTS.md` → `TOKENSAVER.md` |
| **Roo Code** | `AGENTS.md` → `TOKENSAVER.md` |

</details>


### 快速开始

<details>
<summary><b>Claude Code（一行安装）</b></summary>

```bash
git clone https://github.com/thinkofuture/TokenSaver.git ~/.claude/skills/tokensaver
```

在任意项目中运行：

```
/tokensaver
```

TokenSaver 先评估项目现状，创建 `.tokensaver/` 和 `TOKENSAVER.md`。适配器文件只在需要时才创建——以追加方式，绝不覆盖。

</details>


<details>
<summary><b>其他工具</b></summary>

在第一个 prompt 里加上：

> 先读 `TOKENSAVER.md` 和 `.tokensaver/` 理解这个项目，再开始写代码。

就这样。你的 AI 助手不再从零开始。

</details>


**日常使用：**

```bash
# 开始工作 — TOKENSAVER.md 自动加载上下文
claude

# 会话结束 — 只刷新变化的部分
claude "Update project context with today's changes"
```


### 常见问题

<details>
<summary><b>只支持 Claude Code 吗？</b></summary>

不是。Claude Code 有一等 Skill 支持，但 `.tokensaver/` 就是普通 Markdown。Cursor、Codex、Gemini CLI、Aider、Windsurf、Cline、Roo Code 都能直接用。

</details>


<details>
<summary><b>会替代我的 README 或文档吗？</b></summary>

不会。TokenSaver 是给 AI 看的，不是给人看的。保留你的 README、wiki 和 API 文档——它们面向不同的读者。

</details>


<details>
<summary><b>这不是让 AI 读更多文件吗？</b></summary>

正相反。~1,500 tokens 的结构化上下文替代 ~15,000 tokens 的盲目扫描。读得更少，启动更快。

</details>


<details>
<summary><b>.tokensaver/ 要提交到仓库吗？</b></summary>

要。它就是设计来跟着代码一起版本管理的——整个团队的 AI 都能受益。

</details>


<details>
<summary><b>内容过时了怎么办？</b></summary>

刷新一下。每次会话后花两分钟更新受影响的部分。或者让 AI 自查："Check if the project context is still accurate."

</details>


### 示例 & 文档

- 示例：[Next.js SaaS](examples/nextjs-example.md) · [React Native](examples/react-native-example.md) · [AI Agent Framework](examples/saas-example.md)
- 文档：[设计哲学](docs/philosophy.md) · [系统架构](docs/memory-system.md) · [最佳实践](docs/best-practices.md)


### License

MIT © 2026
