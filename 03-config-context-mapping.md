# Config & context file mapping

> 🚦 **Legend:** 🟢 identical · 🟡 renamed / moved · 🔴 no direct 1:1 (workaround)

| 🚦 | Concept | Claude Code | Copilot CLI |
| --- | --- | --- | --- |
| 🟡 | Repo instructions | `CLAUDE.md` | `AGENTS.md` + `.github/copilot-instructions.md` |
| 🟡 | Path-scoped instructions | CLAUDE.md nesting | `.github/instructions/**/*.instructions.md` |
| 🟡 | Custom commands/skills | `.claude/commands/*.md`, `.claude/skills/<n>/SKILL.md` | `SKILL.md` via `copilot skill` (list/add/remove) |
| 🟡 | User-level agents | `~/.claude/agents` | `~/.copilot/agents` |
| 🟡 | Repo-level agents | `.claude/agents` | `.github/agents` |
| 🟡 | Org/enterprise agents | enterprise settings | `.github-private/agents` |
| 🟢 | Hooks | Claude hooks | Copilot hooks |
| 🟡 | Home/config dir | `~/.claude`, `~/.claude.json` | `~/.copilot` (`COPILOT_HOME`) |
| 🟢 | MCP servers | `claude mcp`, `.mcp.json` | preconfigured GitHub MCP; `~/.copilot/mcp-config.json` |
| 🟡 | Headless auth | `claude setup-token` / OAuth token | `COPILOT_GITHUB_TOKEN` / `GH_TOKEN` / `GITHUB_TOKEN` |

---
