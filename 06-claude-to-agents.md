# `CLAUDE.md` ↔ `AGENTS.md` and other "will my stuff just work?" answers

The good news for a skeptical team: **most Claude repo investments are plain files with portable formats.** Practical, real-world guidance:

### Instruction / memory files (`CLAUDE.md` → `AGENTS.md`)
- **Same syntax, different filename.** Both are **freeform natural-language Markdown** — no special schema. Your `CLAUDE.md` content ports **verbatim**; only the file GitHub *looks for* differs.
- Copilot CLI reads **`AGENTS.md`** (repo root, and nested `AGENTS.md` down the tree) plus **`.github/copilot-instructions.md`**; `CLAUDE.md` is the root-level alternative, not a nested path-scoped equivalent.
- **Keep one source of truth** — symlink instead of copy:
```bash
  ln -s CLAUDE.md AGENTS.md     # edits to CLAUDE.md flow to AGENTS.md automatically
```
- A common migration pattern: *use `AGENTS.md` and make `CLAUDE.md` a symlink* so both toolchains coexist during migration.

### Rules / "load an MD when a certain file is touched"
- Claude nests `CLAUDE.md` by directory. Copilot's equivalent is **path-scoped instruction files**: `.github/instructions/**/*.instructions.md` with a frontmatter **`applyTo:` glob**.
```markdown
 ---
  applyTo: "src/payments/**"
  ---
  Always use the money type; never floats for currency.
```
- This is the direct answer to "we have rules files that trigger on certain paths" — it's a supported, first-class feature.

### MCP — does it work as-is?
- **Yes — MCP is a shared open standard.** The same MCP servers you run under Claude work under Copilot; you just **re-declare them** in Copilot's config.
- Move server definitions from Claude's `.mcp.json` into **`~/.copilot/mcp-config.json`** (very similar JSON shape), or add interactively with **`copilot mcp` / `/mcp add`**.
- **GitHub MCP is preinstalled.** Newer CLI also supports \*\*OAuth-based MCP auth, mid-session enable/disable, auto token recovery, and `/mcp list`.\*\*

### Custom commands & skills
- `.claude/commands/*.md` and `.claude/skills/<name>/SKILL.md` → Copilot **skills** via `copilot skill add` (SKILL.md format). In Copilot, those live under `.github/skills` or `~/.copilot/skills`, and reusable/parameterized prompts carry over as skills.

### Shared repo settings
- Project-level `.claude/settings.json` and `.claude/settings.local.json` map to Copilot's repository settings files. The shared subset includes values such as `companyAnnouncements`, `disableAllHooks`, `enabledPlugins`, `extraKnownMarketplaces`, and `hooks`.

### Subagents
- `.claude/agents/*` → **`.github/agents/*.md`** (Markdown + frontmatter). User-level lives in `~/.copilot/agents`, org-level in `.github-private/agents`. Newer agents can set **reasoning effort, response budget, and subagent concurrency/depth**.

### Hooks
- Lifecycle hooks exist on both sides — **re-declare** your pre/post-tool gates (lint/test/policy) in Copilot's hooks.

### Sessions & memory
- Multi-session management, `--continue`/`--resume`, and history retention are all present. For Claude's local memory, evaluate **Copilot Memory** as the interim equivalent against your specific requirement.

> **Field tip:** the fastest migration is (1) symlink `CLAUDE.md`→`AGENTS.md`, (2) copy MCP servers into `mcp-config.json`, (3) move subagents to `.github/agents/`, (4) `skill add` your top 3 commands. That covers most day-to-day workflows in well under an afternoon.
>
> **When something doesn't port:** ask Copilot to migrate it — e.g. `copilot -p "convert this Claude rules file into a Copilot .instructions.md with the right applyTo globs"`.

---
