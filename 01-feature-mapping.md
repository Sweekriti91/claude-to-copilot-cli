# Feature mapping (concept-by-concept)

> 🚦 **Legend:** 🟢 identical · 🟡 renamed / moved · 🔴 no direct 1:1 (workaround)

| 🚦 | Capability | Claude Code CLI | GitHub Copilot CLI |
| --- | --- | --- | --- |
| 🟢 | Interactive TUI | `claude` | `copilot` |
| 🟢 | Headless / scriptable | `claude -p` (`--print`) + `--output-format json` | `copilot -p` (programmatic sessions) |
| 🟢 | Continue / resume | `-c`, `-r <id>` | `--continue`/`-c`, `--resume`, `/resume` picker |
| 🟢 | Model selection | `--model`, `/model` | `--model`, `/model` |
| 🟡 | Specialized agents | subagents, `/agents`, `--agent`, `--agents` (JSON) | custom agents (`/agent`, `--agent`), user/repo/org dirs |
| 🟡 | Deep multi-agent / "ultra code mode" | `ultracode` / ultrathink | autopilot + custom agents + Rubber Duck + Research agent (no plan-as-code 1:1) |
| 🟡 | Multi-agent code review | multi-turn review, `/code-review`, `claude ultrareview` | agentic code review + Code Review agent + Rubber Duck |
| 🟡 | Repeatable scripted workflows | skills, `.claude/commands/*.md`, `/name` | skills (`SKILL.md`), custom agents, hooks |
| 🟢 | Lifecycle automation | hooks | hooks |
| 🟢 | Tool ecosystem | MCP (`claude mcp`, `/mcp`) | MCP — **GitHub MCP preconfigured** (`copilot mcp`, `/mcp add`) |
| 🟢 | Multi-model / vendor | Anthropic (+ Bedrock/Vertex/gateway) | GPT-5.x, Claude, Gemini, Kimi, etc. (multi-vendor in one CLI) |
| 🟡 | Autonomy levels | permission modes, `--dangerously-skip-permissions` | plan → autopilot; `--allow-all`/`--yolo`; sandbox |
| 🟢 | Sandbox / isolation | sandboxing | `/sandbox enable` (local), `copilot --cloud` |
| 🟢 | Background / parallel | agent view, background sessions | background tasks, worktree-native agents, `--cloud` |
| 🟢 | Cost / usage visibility | `/cost`, `/usage` | `/usage` (credits + per-model tokens), `/context` |
| 🟢 | Context files | `@file` | `@file`, `#issue/PR`, images/PDFs (Vision GA), voice |
| 🟡 | Scheduling | (n/a) | `/every`, `/after` |
| 🟢 | Diagnostics | `claude doctor`, `/doctor` | `/diagnose`, `/settings`, `copilot help`, `?` |
| 🟡 | Shell completion | (n/a documented) | `copilot completion SHELL` (`bash`, `zsh`, `fish`) |
| 🟡 | Experimental / fast-moving features | (varies) | `/security-review`, `/research`, `/rubber-duck`, `/fleet`, `/fork`, `/worktree`, `/move` |

---
