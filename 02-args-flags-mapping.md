# Arguments / flags mapping (the core cheat sheet)

> 🚦 **Legend:** 🟢 identical · 🟡 renamed / moved · 🔴 no direct 1:1 (workaround)

| 🚦 | Task | Claude Code | Copilot CLI |
| --- | --- | --- | --- |
| 🟢 | Start interactive | `claude` | `copilot` |
| 🟢 | Start with a prompt | `claude "query"` | `copilot "query"` |
| 🟢 | One-shot / headless / scripted | `claude -p "query"` | `copilot -p "query"` |
| 🟢 | Pipe stdin | `cat f \| claude -p "q"` | `cat f \| copilot -p "q"` |
| 🟢 | Continue most recent | `claude -c` / `--continue` | `copilot --continue` / `-c` |
| 🟡 | Resume by id/name | `claude -r "<id>" "q"` | `copilot --resume` (picker) / `/resume` |
| 🟢 | Choose model | `claude --model opus` | `copilot --model <model>` |
| 🟢 | Run as a named agent | `claude --agent my-agent` | `copilot --agent=my-agent -p "..."` |
| 🟡 | Define agents inline | `claude --agents '{...}'` | define file(s) in `.github/agents/*.md` |
| 🟡 | Allow specific tools (no prompt) | `claude --allowedTools "Bash(git log *)" "Read"` | `copilot --allow-tool '<tool>'` (repeatable) |
| 🟡 | Deny specific tools | `--disallowedTools ...` | `copilot --deny-tool '<tool>'` |
| 🟡 | Allow everything (dangerous) | `claude --dangerously-skip-permissions` | `copilot --allow-all-tools` (or `--allow-all` / `--yolo`) |
| 🟡 | Permission / plan mode | `--permission-mode plan` | Plan/Autopilot via Shift+Tab |
| 🟢 | Add working directory | `claude --add-dir ../lib` | `copilot --add-dir <dir>` / `/add-dir` |
| 🔴 | Extra system prompt | `--append-system-prompt "..."` | custom instructions (AGENTS.md / copilot-instructions.md) |
| 🟡 | MCP config | `--mcp-config <file>` | `~/.copilot/mcp-config.json`; `copilot mcp` / `/mcp add` |
| 🟡 | Enable experimental features | (n/a) | `copilot --experimental` / `/experimental on` |
| 🟡 | Allow / deny URLs | (n/a) | `copilot --allow-url` / `--deny-url` |
| 🟡 | Local sandbox toggle | (n/a) | `copilot --sandbox` / `--no-sandbox` |
| 🟢 | Cloud / web session | `claude --cloud "task"` | `copilot --cloud` |
| 🟢 | Update | `claude update` | `copilot update` |
| 🟡 | Version | `claude --version` | `copilot version` |

**Autonomy spectrum:** Claude `plan` → default → `--dangerously-skip-permissions` ≈ Copilot plan → autopilot → `--allow-all`/`--yolo` (+ sandbox, `--experimental` for fast-moving features).

---
