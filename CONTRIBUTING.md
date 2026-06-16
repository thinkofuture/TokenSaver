# Contributing to TokenSaver

Thanks for your interest! TokenSaver is an open-source, agent-agnostic project context system that saves AI coding tokens — with first-class Claude Code Skill support.

## What's Most Valuable

### Share Real-World Experience

Used TokenSaver on a project? Open an issue with what worked, what was confusing, and what you changed. Especially valuable: experience using TokenSaver with non-Claude-Code tools (Cursor, Codex, Gemini CLI, Aider, etc.).

### Add Examples

Real-world example context systems are the best contributions. See `examples/` for format — keep them lightweight and realistic.

### Improve the Skill

`SKILL.md` defines the workflow. If you find steps that are unclear, missing patterns, or better approaches, open a PR.

### Add Tool Adapters

Know how to wire TokenSaver into a tool not yet listed? Contribute an entry point template or integration guide.

## Guidelines

- **Keep it focused.** One change per PR.
- **Test with real AI coding sessions** when modifying SKILL.md.
- **Match the tone.** Concise, engineering-focused, agent-agnostic.
- **Update related files.** If you change SKILL.md, check if README or docs need updates.

## Development

No build step, no dependencies. This is a Markdown project.

```bash
# Test the skill
claude "Read SKILL.md and follow its instructions for this project"
```

## License

By contributing, you agree your contributions will be licensed under MIT.
