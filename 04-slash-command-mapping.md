# Slash-command & interactive mapping

> 🚦 **Legend:** 🟢 identical · 🟡 renamed / moved · 🔴 no direct 1:1 (workaround)

| 🚦 | Action | Claude Code | Copilot CLI |
| --- | --- | --- | --- |
| 🟢 | Inline shell | `!cmd` | `!cmd` / `!` shell mode |
| 🟢 | Add file to context | `@file` | `@file` |
| 🟢 | Reference issue/PR | (paste/MCP) | `#123` |
| 🟢 | Compact context | `/compact` | `/compact` (auto at 95%) |
| 🟢 | Context overview | `/context` | `/context` |
| 🟢 | Cost/usage | `/cost`, `/usage` | `/usage` |
| 🟢 | Plan vs autonomous session | Shift+Tab (default / plan) | Shift+Tab (interactive → plan → autopilot), `/plan`, `/autopilot` |
| 🟡 | Permission mode | Manual / Auto / bypass | `/permissions` (Manual / Assisted / Allow all) |
| 🟡 | Automatic model choice | (separate from Claude Auto permissions) | `/model` → **Auto** |
| 🟡 | Code review | `/code-review`, `claude ultrareview` | `/review`, `/security-review`, Code Review agent |
| 🟡 | Deep research / second opinion | (n/a) | `/research`, `/rubber-duck` |
| 🟢 | MCP panel | `/mcp` | `/mcp`, `/mcp add`, `/mcp list` |
| 🟢 | Change dir | `--add-dir` | `/cwd`, `/cd`, `/add-dir` |
| 🟡 | Session / worktree orchestration | (n/a) | `/diff`, `/resume`, `/fork`, `/worktree`, `/move` |
| 🟡 | Customize the CLI | settings files | `/settings [KEY VALUE]`, `/instructions`, `/skills`, `/plugin` |
| 🟡 | Schedule | (n/a) | `/every`, `/after` |
| 🟡 | Experimental / automation | Claude Auto mode | `/settings experimental true`, `/permissions` → Assisted, `/fleet`, `/search` |
| 🟡 | Open desktop app | Claude Desktop | `/app` or `copilot app` |
| 🟢 | Sandbox | sandboxing | `/sandbox enable`, `--cloud` |
| 🟢 | Diagnostics | `/doctor` | `/diagnose`, `/env`, `/help` |

---
