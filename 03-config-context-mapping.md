# Config & context file mapping

> 🚦 **Legend:** 🟢 identical · 🟡 renamed / moved · 🔴 no direct 1:1 (workaround)

| 🚦 | Concept | Claude Code | Copilot CLI |
| --- | --- | --- | --- |
| 🟡 | Repo instructions | `CLAUDE.md` / `AGENTS.md` (root or nearest file) | `AGENTS.md` + `.github/copilot-instructions.md` |
| 🟡 | Path-scoped instructions | `CLAUDE.md` nesting | `.github/instructions/**/*.instructions.md` with `applyTo` globs |
| 🟡 | Custom commands/skills | `.claude/commands/*.md`, `.claude/skills/<n>/SKILL.md` | `SKILL.md` via `copilot skill` and `.github/skills`, `~/.copilot/skills` |
| 🟡 | User-level agents | `~/.claude/agents` | `~/.copilot/agents` |
| 🟡 | Repo-level agents | `.claude/agents` | `.github/agents` |
| 🟡 | Org/enterprise agents | enterprise settings | `.github-private/agents` |
| 🟢 | Hooks | Claude hooks | Copilot hooks |
| 🟡 | User settings | `~/.claude/settings.json` | `~/.copilot/settings.json` |
| 🟡 | Repo settings | `.claude/settings.json` | `.github/copilot/settings.json` |
| 🟡 | Local repo override | `.claude/settings.local.json` | `.github/copilot/settings.local.json` |
| 🟡 | Shared repo settings subset | `.claude/settings.json`, `.claude/settings.local.json` | `.github/copilot/settings.json`, `.github/copilot/settings.local.json` |
| 🟡 | Home/config dir | `~/.claude`, `~/.claude.json` | `~/.copilot` (`COPILOT_HOME`) |
| 🟢 | MCP servers | `claude mcp`, `.mcp.json` | preconfigured GitHub MCP; `~/.copilot/mcp-config.json` |
| 🟡 | Headless auth | `claude setup-token` / OAuth token | `COPILOT_GITHUB_TOKEN` / `GH_TOKEN` / `GITHUB_TOKEN` |

---
