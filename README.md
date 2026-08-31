# Claude Code → GitHub Copilot CLI — Migration Guide

A practical, side-by-side reference for engineers moving day-to-day workflows and agentic pipelines from **Claude Code CLI** to **GitHub Copilot CLI**. The mappings are CLI-first, with **GitHub Copilot app** steps where the same setting is exposed there.

**Tip:** point your Copilot **custom agents / instructions** at these files, or clone the repo as a local reference your agent can read during a migration.

> **Last checked:** August 31, 2026, against the current GitHub Docs and Copilot CLI 1.0.82 release notes.

## “Auto” does not mean one thing

People arriving from Claude Code often expect **Auto** to be one mode. In Copilot, three independent controls use similar language:

| Control | What it controls | What it does **not** control | CLI | GitHub Copilot app |
| --- | --- | --- | --- | --- |
| **Autopilot** | **Agent/session mode:** whether Copilot keeps taking model turns and working through a task without waiting for another prompt | Which model is used, or whether a tool is allowed to run | Press `Shift+Tab`, run `/autopilot`, or start with `copilot --autopilot` | Choose **Autopilot** from the **session mode** dropdown |
| **Assisted approvals** *(experimental; older builds called this `/allow-all auto`)* | **Permission mode:** a safety judge auto-approves tool calls it considers safe and prompts for the rest | Whether the agent continues to the next step, or which model is used | Enable experimental features, then choose **Assisted** in `/permissions`; or start with `copilot --experimental --assisted-approval` | Enable experimental features, then choose **Assisted** in the session’s permission control or `/permissions` |
| **Auto** in the model picker | **Model selection:** Copilot routes work to an eligible model based on task complexity, model health, availability, plan, and policy | Agent autonomy or tool permissions | Run `/model` and choose **Auto**, or start with `copilot --model auto` | Choose **Auto** from the **model** dropdown |

The short version:

> **Autopilot decides when the agent stops. Permissions decide whether a tool may run. Auto model selection decides which model answers.**

### How this maps from Claude Code

Claude Code’s **Auto mode** is a permission classifier. Its closest Copilot equivalent is **Assisted approvals**, not Copilot **Autopilot** and not the **Auto** model picker.

| Claude Code | Closest Copilot concept |
| --- | --- |
| Auto mode | Assisted approvals *(experimental)* |
| `--dangerously-skip-permissions` | `--allow-all` / `/allow-all` *(blanket permission; use only in a trusted sandbox)* |
| Long-running autonomous work | Autopilot |
| Automatic model choice | Auto model selection |

Autopilot and Assisted approvals are often useful together, but neither silently enables the other. Autopilot with manual permissions can stall on approvals; Assisted approvals in Interactive mode still return control after a normal turn.

### Enable experimental features

**Copilot CLI**

```bash
# Persistent setting from an interactive session
/settings experimental true

# Or launch/enable from the shell
copilot --experimental

# Then select the assisted permission mode
/permissions
```

You can also open `/settings`, search for `experimental`, and toggle it there. Disable it with `/settings experimental false` or `copilot --no-experimental`.

**GitHub Copilot app**

1. Open or create an agent session.
2. Enter `/settings`, search for `experimental`, and enable it. The inline form `/settings experimental true` also works in the session prompt.
3. Open the session permission control (or run `/permissions`) and choose **Assisted**.

If **Assisted** is missing or locked, update the App/CLI first and check your organization’s managed policy. Experimental features can change names or graduate without notice.

For a persistent startup combination in current CLI releases:

```json
{
  "experimental": true,
  "defaultMode": "autopilot",
  "defaultPermissionMode": "assisted",
  "model": "auto"
}
```

These settings are independent; only enable the combination you actually want. See GitHub’s docs for [Autopilot](https://docs.github.com/copilot/concepts/agents/copilot-cli/autopilot), [auto model selection](https://docs.github.com/copilot/concepts/models/auto-model-selection), and [App agent sessions](https://docs.github.com/copilot/how-tos/github-copilot-app/agent-sessions).

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
