# Claude Code → GitHub Copilot CLI — Migration Guide

A practical, side-by-side reference for engineers moving day-to-day workflows and agentic pipelines from **Claude Code CLI** to **GitHub Copilot CLI**. Everything here is scoped to the **Copilot CLI** (not the IDE or app).

**Tip:** point your Copilot **custom agents / instructions** at these files, or clone the repo as a local reference your agent can read during a migration.

## 🚦 Legend
- 🟢 **identical** — same command/flag, no change needed
- 🟡 **renamed / moved** — same idea, different name or location
- 🔴 **no direct 1:1** — needs a workaround or is a known gap

> ⚠️ A few exact flag strings (e.g. `--allow-tool` pattern syntax) evolve — verify with `copilot help permissions` / `?` before relying on them.

## Contents
1. [Feature mapping](01-feature-mapping.md)
2. [Arguments / flags mapping](02-args-flags-mapping.md)
3. [Config & context file mapping](03-config-context-mapping.md)
4. [Slash-command & interactive mapping](04-slash-command-mapping.md)
5. [Command translation recipes](05-command-recipes.md)
6. [CLAUDE.md ↔ AGENTS.md & "will my stuff just work?"](06-claude-to-agents.md)
7. [Official docs](07-official-docs.md)
8. [Announcements, blogs & talks](08-announcements-and-talks.md)
9. [Headless mode](09-headless-mode.md)

---
*Community reference for terminal-native AI coding migrations. Not an official GitHub product doc.*
