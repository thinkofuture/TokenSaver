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

<br>

---

<br>

<a id="english"></a>

## English

<br>

### Why TokenSaver?

Your AI agent has no project memory.

Every new session, it opens the same files again: `package.json`, routes, configs, docs, random files under `src/`…

<br>

That's fine once.<br>
It's expensive forever.

TokenSaver reads the important bits once, then hands your agent a short cheat sheet it can reuse.

```
Without TokenSaver →  a large repeated repo scan, every session
With TokenSaver    →  a small reusable read, once
```

<br>

### What it does

TokenSaver reads your project's key files, then creates:

- **`TOKENSAVER.md`** — a short entry point your agent reads first
- **`.tokensaver/`** — a focused context folder with 7 lightweight Markdown files

Your agent gets oriented from a few small files instead of scanning the whole repo. It only opens source files when it actually needs the details.

> TokenSaver replaces expensive full-repo scanning. It's not another layer on top — it's a shortcut.

<br>

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

**`TOKENSAVER.md`** — the universal entry point. Adapters point AI tools here first.

**Thin adapters** — small pointers for tools that auto-load specific files:

| Adapter | For |
|---------|-----|
| `CLAUDE.md` | Claude Code auto-load |
| `AGENTS.md` | Codex, Copilot Coding Agent, Cline, Windsurf, Roo Code |
| `GEMINI.md` | Gemini CLI |
| `.cursor/rules/tokensaver.mdc` | Cursor |
| `.github/copilot-instructions.md` | VS Code Copilot |

Changes are wrapped in `<!-- tokensaver:start -->` / `<!-- tokensaver:end -->` — your existing config stays untouched.

<br>

### How it works with your tools

TokenSaver is **agent-agnostic**. `TOKENSAVER.md` is a plain Markdown file. How tools pick it up depends on their read mechanism:

- Tools that auto-load instruction files (Claude Code, Cursor, Codex…) → **thin adapters**
- Tools that don't auto-load → **drop a one-liner in your first prompt**

<details>
<summary><b>Tool compatibility</b></summary>

| Tool | How it loads TokenSaver |
|------|------------------------|
| **Claude Code** | First-class `/tokensaver` Skill + `CLAUDE.md` → `TOKENSAVER.md` |
| **Cursor** | `.cursor/rules/tokensaver.mdc` → `TOKENSAVER.md` |
| **Codex / OpenAI** | `AGENTS.md` → `TOKENSAVER.md` |
| **GitHub Copilot Coding Agent** | `AGENTS.md` → `TOKENSAVER.md` |
| **Gemini CLI** | `GEMINI.md` → `TOKENSAVER.md` |
| **VS Code Copilot** | `.github/copilot-instructions.md` → `TOKENSAVER.md` |
| **Aider** | Reads `TOKENSAVER.md` directly |
| **Windsurf** | `AGENTS.md` → `TOKENSAVER.md` |
| **Cline** | `AGENTS.md` → `TOKENSAVER.md` |
| **Roo Code** | `AGENTS.md` → `TOKENSAVER.md` |

</details>

<br>

### Quick Start

**The only file you need is `SKILL.md`.** One command, one file — that's the install.

<details>
<summary><b>Claude Code — install in 5 seconds</b></summary>

```bash
mkdir -p ~/.claude/skills/tokensaver && \
curl -sL https://raw.githubusercontent.com/thinkofuture/TokenSaver/main/SKILL.md \
  -o ~/.claude/skills/tokensaver/SKILL.md
```

That's it. Now in any project:

```
/tokensaver
```

TokenSaver scans your project, creates `.tokensaver/` and `TOKENSAVER.md`, then adds thin adapter blocks where useful — no manual prompts in most setups.

Thin adapter blocks added to:

| Tool | Adapter file |
|------|-------------|
| Claude Code | `CLAUDE.md` |
| Cursor | `.cursor/rules/tokensaver.mdc` |
| Codex, Copilot Coding Agent, Windsurf, Cline, Roo Code | `AGENTS.md` |
| Gemini CLI | `GEMINI.md` |
| VS Code Copilot | `.github/copilot-instructions.md` |

All wrapped in `<!-- tokensaver:start/end -->` blocks — your existing config stays untouched.

</details>

<details>
<summary><b>Clone the full repo (docs, examples, or contributing)</b></summary>

```bash
git clone https://github.com/thinkofuture/TokenSaver.git
```

Useful when you want to browse examples, read the philosophy docs, or send a PR.

</details>

**Daily use:**

```bash
# Start work — adapters point to TOKENSAVER.md
claude

# End of session — refresh what changed
claude "Update project context with today's changes"
```

<br>

### FAQ

<details>
<summary><b>Is this Claude Code only?</b></summary>

No. Claude Code gets a first-class Skill, but `.tokensaver/` is plain Markdown. Every major AI coding tool has a thin adapter file that points to `TOKENSAVER.md` — `/tokensaver` creates them all in one go.

</details>

<details>
<summary><b>Does it replace my README or docs?</b></summary>

No. TokenSaver is for AI agents, not humans. Keep your README, wiki, and API docs — they serve a different audience.

</details>

<details>
<summary><b>Won't this make the agent read more files?</b></summary>

Opposite. A few small structured files replace the usual blind repo scan. Less reading, faster starts.

</details>

<details>
<summary><b>Should I commit .tokensaver/?</b></summary>

Yes. It's designed to be versioned alongside your code — the whole team's agents benefit from it.

</details>

<details>
<summary><b>What if it goes stale?</b></summary>

Refresh it. Two minutes after a session updates the changed files. Or ask your agent: "Check if the project context is still accurate."

</details>

<br>

### Examples & Docs

- Examples: [Next.js SaaS](examples/nextjs-example.md) · [React Native](examples/react-native-example.md) · [AI Agent Framework](examples/saas-example.md)
- Docs: [Philosophy](docs/philosophy.md) · [Architecture](docs/memory-system.md) · [Best Practices](docs/best-practices.md)

### License

MIT © 2026

<br>

---

<br>

<a id="中文"></a>

## 中文

<br>

### 为什么需要 TokenSaver？

别再花 token 让 AI 重读你的仓库。<br>
TokenSaver 把它压缩成一份小抄。

每次新会话，AI 都从零开始翻你的项目：`package.json`、路由、配置、docs、`src/` 下面一堆文件……

<br>

第一次还行。<br>
每次都这样，又慢又烧钱。

```
没有 TokenSaver →  每次都要大规模扫描一遍
使用 TokenSaver →  一次小小的可复用读取
```

<br>

### 它做什么？

TokenSaver 先读项目关键文件，然后生成：

- **`TOKENSAVER.md`** — 一个简短入口，AI 优先读这里
- **`.tokensaver/`** — 一个轻量上下文目录，7 个 Markdown 文件

AI 读几个小文件就能了解项目全貌，不需要到处翻源码。只有在真正需要实现细节时，才打开对应文件。

> TokenSaver 替代昂贵的全仓库扫描，不是在已有配置上额外加一层——它是捷径。

<br>

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

**`TOKENSAVER.md`** — 统一入口，适配器会引导 AI 工具先读这里。

**薄适配器** — 给会自动读取特定文件的工具使用：

| 适配器 | 适用工具 |
|--------|---------|
| `CLAUDE.md` | Claude Code 自动加载 |
| `AGENTS.md` | Codex、Copilot Coding Agent、Cline、Windsurf、Roo Code |
| `GEMINI.md` | Gemini CLI |
| `.cursor/rules/tokensaver.mdc` | Cursor |
| `.github/copilot-instructions.md` | VS Code Copilot |

修改用 `<!-- tokensaver:start -->` / `<!-- tokensaver:end -->` 包裹——你已有的配置不动分毫。

<br>

### 和你的工具怎么配合？

TokenSaver 从设计上就是**工具无关**的。`TOKENSAVER.md` 就是普通 Markdown。不同工具的衔接方式：

- 有自动加载机制的工具（Claude Code、Cursor、Codex…）→ **薄适配器**
- 没有自动加载的工具 → **在第一个 prompt 里加一行**

<details>
<summary><b>工具兼容性</b></summary>

| 工具 | 如何加载 TokenSaver |
|------|-------------------|
| **Claude Code** | 一等 `/tokensaver` Skill + `CLAUDE.md` → `TOKENSAVER.md` |
| **Cursor** | `.cursor/rules/tokensaver.mdc` → `TOKENSAVER.md` |
| **Codex / OpenAI** | `AGENTS.md` → `TOKENSAVER.md` |
| **GitHub Copilot Coding Agent** | `AGENTS.md` → `TOKENSAVER.md` |
| **Gemini CLI** | `GEMINI.md` → `TOKENSAVER.md` |
| **VS Code Copilot** | `.github/copilot-instructions.md` → `TOKENSAVER.md` |
| **Aider** | 直接读取 `TOKENSAVER.md` |
| **Windsurf** | `AGENTS.md` → `TOKENSAVER.md` |
| **Cline** | `AGENTS.md` → `TOKENSAVER.md` |
| **Roo Code** | `AGENTS.md` → `TOKENSAVER.md` |

</details>

<br>

### 快速开始

**你只需要一个文件：`SKILL.md`。** 一行命令，一个文件——安装完成。

<details>
<summary><b>Claude Code — 五秒安装</b></summary>

```bash
mkdir -p ~/.claude/skills/tokensaver && \
curl -sL https://raw.githubusercontent.com/thinkofuture/TokenSaver/main/SKILL.md \
  -o ~/.claude/skills/tokensaver/SKILL.md
```

搞定。在任意项目中运行：

```
/tokensaver
```

TokenSaver 先评估项目，创建 `.tokensaver/` 和 `TOKENSAVER.md`，并在合适的位置加入薄适配器区块——大多数场景不需要手动粘贴 prompt。

薄适配器区块加在：

| 工具 | 适配器文件 |
|------|----------|
| Claude Code | `CLAUDE.md` |
| Cursor | `.cursor/rules/tokensaver.mdc` |
| Codex、Copilot Coding Agent、Windsurf、Cline、Roo Code | `AGENTS.md` |
| Gemini CLI | `GEMINI.md` |
| VS Code Copilot | `.github/copilot-instructions.md` |

所有修改都用 `<!-- tokensaver:start/end -->` 包裹——你已有的配置不动分毫。

</details>

<details>
<summary><b>克隆完整仓库（查看文档、示例或参与开发）</b></summary>

```bash
git clone https://github.com/thinkofuture/TokenSaver.git
```

适合想浏览示例、阅读设计哲学文档或提交 PR 的场景。

</details>

**日常使用：**

```bash
# 开始工作 — 适配器指向 TOKENSAVER.md
claude

# 会话结束 — 只刷新变化的部分
claude "Update project context with today's changes"
```

<br>

### 常见问题

<details>
<summary><b>只支持 Claude Code 吗？</b></summary>

不是。Claude Code 有一等 Skill 支持，但 `.tokensaver/` 就是普通 Markdown。所有主流 AI 编码工具都有对应的薄适配器文件指向 `TOKENSAVER.md`——`/tokensaver` 一次全搞定。

</details>

<details>
<summary><b>会替代我的 README 或文档吗？</b></summary>

不会。TokenSaver 是给 AI 看的，不是给人看的。保留你的 README、wiki 和 API 文档——它们面向不同的读者。

</details>

<details>
<summary><b>这不是让 AI 读更多文件吗？</b></summary>

正相反。用几个小文件替代每次的盲目扫描——读得更少，启动更快。

</details>

<details>
<summary><b>.tokensaver/ 要提交到仓库吗？</b></summary>

要。它就是设计来跟着代码一起版本管理的——整个团队的 AI 都能受益。

</details>

<details>
<summary><b>内容过时了怎么办？</b></summary>

刷新一下。每次会话后花两分钟更新受影响的部分。或者让 AI 自查："Check if the project context is still accurate."

</details>

<br>

### 示例 & 文档

- 示例：[Next.js SaaS](examples/nextjs-example.md) · [React Native](examples/react-native-example.md) · [AI Agent Framework](examples/saas-example.md)
- 文档：[设计哲学](docs/philosophy.md) · [系统架构](docs/memory-system.md) · [最佳实践](docs/best-practices.md)

### License

MIT © 2026
