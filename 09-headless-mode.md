# Headless mode

Practical guidance for running **GitHub Copilot CLI** in non-interactive,
scripted, or CI-style workflows with `-p`.

## Quick headless recipes

```bash
# 1. Quick one-shot in a script
copilot -p "summarize the failing tests" -s

# 2. Pipe a file in headless mode
cat error.log | copilot -s

# 3. Compose with other Unix tools
git diff | copilot -p "review this diff for security issues"
copilot -p "generate release notes" < commits.txt

# 4. Unattended run (full trust)
copilot -p "apply the migration and run tests" --allow-all-tools

# 5. Granular tool trust
copilot -p "clean up branches" \
  --allow-tool 'shell(git:*)' \
  --deny-tool 'shell(git push)'

# 6. Pick a model per step
copilot -p "draft the refactor" --model claude-sonnet-5
copilot -p "final review pass" --model gpt-5.6

# 7. Run as a named custom agent
copilot --agent=reviewer -p "review the diff on this branch"

# 8. Continue / resume a session
copilot --continue
copilot --resume

# 9. Add extra working directories
copilot --add-dir ../shared-lib --add-dir ../protos
```

## Prompt input and output patterns

Headless mode supports both direct prompts and stdin:

```bash
# Direct prompt
copilot -p "Summarize the latest commit" -s

# Prompt from stdin
echo "Summarize the latest commit" | copilot -s

# File or command output from stdin
git diff | copilot -p "Review this diff for bugs" -s
```

If you provide both stdin and `-p`, the explicit `-p` prompt wins and the piped
input is ignored.

For output, there are two especially useful modes:

| Mode | Best for |
| --- | --- |
| `-s` / `--silent` | Plain shell pipelines and capturing simple text |
| `--output-format json` | Wrapper scripts that want structured JSONL output |

```bash
copilot -p "Summarize the diff" -s
copilot -p "Summarize the diff" --output-format json
```

## Safe baseline for automation

For repeatable non-interactive runs, a good default pattern is:

```bash
copilot -p "summarize the diff" \
  -s \
  --no-ask-user \
  --model gpt-5.3-codex \
  --allow-tool='read,shell(git:*)'
```

This keeps output script-friendly, disables follow-up questions, pins the
model, and grants only the minimum permissions needed for a typical read-only
analysis task.

## Experimental mode in programmatic sessions

`-p` does not automatically enable experimental mode; it uses your current
persisted configuration.

For a one-shot prompt:

```bash
copilot --experimental -p "your prompt"
```

`--experimental` enables experimental mode and persists that setting, so later
sessions may remain enabled. To toggle it explicitly from programmatic mode:

```bash
copilot -p "/settings experimental true"
copilot -p "/settings experimental false"
```

Use `/settings` to inspect the exact setting name and available individual
flags. See the [Copilot CLI settings announcement](https://github.blog/changelog/2026-06-11-copilot-cli-configure-everything-from-one-place-with-settings/).

## Settings file locations

Headless runs can inherit configuration from the same settings files used by
interactive runs.

| Scope | Path |
| --- | --- |
| User/global | `~/.copilot/settings.json` |
| Repo-shared | `.github/copilot/settings.json` |
| Repo-local override | `.github/copilot/settings.local.json` |
| Alternate global location | `$COPILOT_HOME/settings.json` |

Use `COPILOT_HOME` when you want a script or CI job to run with an isolated
configuration directory instead of the user's default `~/.copilot`.

## Example settings.json for headless runs

```json
{
  "model": "gpt-5.3-codex",
  "effortLevel": "low",
  "experimental": false,
  "askUser": false,
  "autoUpdate": false,
  "logLevel": "info",
  "allowedUrls": ["github.com", "docs.github.com"],
  "deniedUrls": ["malicious-site.com"]
}
```

Settings precedence is:

1. Built-in defaults
2. User settings
3. Repo settings
4. Local repo settings
5. Environment variables
6. Command-line flags

For headless automation, this means `settings.json` is best for defaults, while
environment variables and CLI flags are best for per-run overrides.

## Generic shell setup with a dedicated settings file

For CI systems or wrapper scripts, you can point Copilot at a dedicated config
directory with `COPILOT_HOME` and place a `settings.json` there.

```bash
# 1. Create an isolated Copilot home for the script or CI job
export COPILOT_HOME="${COPILOT_HOME:-$PWD/.copilot-headless}"
mkdir -p "$COPILOT_HOME"

# 2. Write the settings file Copilot should use
cat > "$COPILOT_HOME/settings.json" <<'EOF'
{
  "model": "gpt-5.3-codex",
  "effortLevel": "low",
  "experimental": false,
  "askUser": false,
  "autoUpdate": false,
  "logLevel": "info"
}
EOF

# 3. Provide auth with an environment variable
export COPILOT_GITHUB_TOKEN=github_pat_...

# 4. Run Copilot in headless mode
copilot -p "summarize the diff" \
  --allow-tool='read,shell(git:*)' \
  --output-format json
```

Use a dedicated `COPILOT_HOME` when you want predictable, isolated config for
automation. Use repo settings files when you want shared defaults checked into
the repository.

## Session persistence and resume behavior

Headless runs still create session data unless you isolate them with a separate
`COPILOT_HOME`.

| Purpose | Command or path |
| --- | --- |
| Continue the most recent session | `copilot --continue` |
| Resume a prior session | `copilot --resume` or `copilot --resume=<session-id>` |
| Force a specific session ID | `copilot --session-id <id>` |
| Session history on disk | `~/.copilot/session-state/` |
| Cross-session index database | `~/.copilot/session-store.db` |

This is useful when you want repeated scripted runs to build on prior context,
but it is often better to use a dedicated `COPILOT_HOME` for isolated,
predictable automation.

## Common shell scripting patterns

Capture output in a variable:

```bash
summary="$(copilot -p 'Summarize the latest commit in one sentence.' -s)"
echo "$summary"
```

Use Copilot in a conditional:

```bash
if copilot -p 'Reply only YES or NO: are there TypeScript errors?' -s | grep -qi '^no$'; then
  echo "No type errors."
else
  echo "Type errors detected."
fi
```

Loop over files:

```bash
for file in src/api/*.ts; do
  copilot -p "Review $file for error handling issues" -s
done
```

## Logs, transcripts, and debugging

Useful locations and flags:

| Purpose | Command or path |
| --- | --- |
| Default log directory | `~/.copilot/logs/` |
| Override log directory | `--log-dir <dir>` |
| Increase verbosity | `--log-level debug` |
| Export transcript to markdown | `--share[=path]` |
| Export transcript to secret gist | `--share-gist` |

```bash
copilot -p "Audit this repository" \
  --log-dir ./copilot-logs \
  --log-level debug \
  --share ./copilot-session.md
```

Use exported transcripts for auditing, post-processing, or attaching results to
another system.

## Permissions cookbook

There are three related mechanisms to understand:

| Mechanism | Where it lives | Best use |
| --- | --- | --- |
| CLI permission flags | command line | Deterministic per-run control in scripts |
| `settings.json` | `~/.copilot/settings.json` or `$COPILOT_HOME/settings.json` | Persistent defaults such as URL allow/deny lists and non-interactive behavior |
| `permissions-config.json` | `~/.copilot/permissions-config.json` or `$COPILOT_HOME/permissions-config.json` | Persisted tool and directory approvals by repository or folder |

### Common CLI permission patterns

| Goal | Example |
| --- | --- |
| Allow all Git subcommands | `--allow-tool='shell(git:*)'` |
| Allow one exact shell command | `--allow-tool='shell(npm test)'` |
| Deny one risky command | `--deny-tool='shell(git push)'` |
| Allow writes to one file name pattern | `--allow-tool='write(README.md)'` |
| Allow one MCP tool | `--allow-tool='github(create_issue)'` |
| Allow one domain | `--allow-url=github.com` |
| Deny one domain | `--deny-url=malicious-site.com` |
| Allow all URLs | `--allow-all-urls` |
| Allow all tools, paths, and URLs | `--allow-all` or `--yolo` |

Two important distinctions:

- `--available-tools` and `--excluded-tools` control **what tools the model can
  see at all**
- `--allow-tool` and `--deny-tool` control **approval behavior** for tools that
  are available

### What can go in settings.json

`settings.json` is useful for persistent defaults around headless behavior, but
it is **not** the place for `--allow-tool` / `--deny-tool` style tool approval
rules.

Good fits for `settings.json` include:

```json
{
  "model": "gpt-5.3-codex",
  "effortLevel": "low",
  "askUser": false,
  "experimental": false,
  "logLevel": "info",
  "allowedUrls": ["github.com", "docs.github.com"],
  "deniedUrls": ["malicious-site.com"]
}
```

This means:

- you **can** persist URL allow/deny behavior in `settings.json`
- you **cannot** express `shell(git:*)` or `write(README.md)` there
- for tool approvals, use CLI flags for per-run control or
  `permissions-config.json` for persisted approvals

### Persisting tool approvals with permissions-config.json

If you want Copilot to remember approved tools or extra directories for a given
repository or working directory, use `permissions-config.json`.

Example:

```json
{
  "locations": {
    "/path/to/repo": {
      "tool_approvals": [
        {
          "kind": "commands",
          "commandIdentifiers": ["git:*", "npm test", "npm run build"]
        },
        {
          "kind": "write"
        }
      ],
      "allowed_directories": ["/path/to/shared-docs"]
    }
  }
}
```

This file is useful for persistent local approvals, but for automation and CI,
explicit CLI flags are usually clearer and more reproducible.

## Real-world automation examples

Common headless use cases worth documenting:

| Use case | Example |
| --- | --- |
| Commit message generation | `copilot -p 'Write a commit message for the staged changes' -s --allow-tool='shell(git:*)'` |
| Code review in a script | `copilot -p '/review the changes on this branch compared to main. Focus on bugs and security issues.' -s --allow-tool='shell(git:*)'` |
| Test or coverage summaries | `copilot -p 'Run the test suite and produce a coverage summary' -s --allow-tool='shell(npm:*),write' --no-ask-user` |
| Documentation generation | `copilot -p 'Generate JSDoc comments for exported functions in src/api/' --allow-tool='write'` |
| Build-log repair workflows | feed logs or a prompt file to `copilot -p` with narrow `shell(...)` permissions |
| Smoke-test / readiness probe | `copilot -p 'Reply with just READY and nothing else.' -s --model gpt-5.3-codex` |
| Wrapper/provider backend | use `copilot -p ... --output-format json` as a subprocess behind another script or tool |

Public examples also show Copilot used in shell hooks, wrapper scripts, and
benchmark harnesses that compare multiple models or run repeated scripted
prompts.

## Headless arguments reference

The core entrypoint for headless mode is `-p` / `--prompt`, but many other
flags can be combined with it.

### Core headless and session flags

| Flag | What it does | Headless note |
| --- | --- | --- |
| `--prompt <text>` | Run a prompt non-interactively and exit | Core headless flag (`-p`) |
| `--silent` | Output only the agent response, with no stats | Best for scripts (`-s`) |
| `--output-format <text\|json>` | Choose plain text or JSONL output | Use `json` for wrappers and parsers |
| `--attachment <path>` | Attach a file to the initial prompt | Only valid in non-interactive mode |
| `--share[=path]` | Export transcript to markdown after completion | Good for audit/reporting |
| `--share-gist` | Export transcript to a secret gist | Good for sharing outputs |
| `--continue` | Resume the most recent session | Useful if scripted runs should keep context |
| `--resume[=value]` | Resume a prior session by ID, name, or prefix | Headless-compatible |
| `--session-id <id>` | Set UUID for a new session or resume an existing one | Good for controlled session reuse |
| `--name <name>` | Name a new session | Helps with traceability |
| `-C <directory>` | Change working directory first | Useful in wrappers |
| `--connect[=sessionId]` | Connect directly to a remote session or task | Advanced session control |

### Model and agent behavior flags

| Flag | What it does | Headless note |
| --- | --- | --- |
| `--model <model>` | Select model | Best to pin in automation |
| `--agent <agent>` | Use a named custom agent | Good for specialized scripted flows |
| `--context <default\|long_context>` | Override context window tier | Per-run override |
| `--effort <level>` / `--reasoning-effort <level>` | Set reasoning effort | Supports `none`, `minimal`, `low`, `medium`, `high`, `xhigh`, `max` |
| `--mode <interactive\|plan\|autopilot>` | Set initial mode | Can be used with headless runs |
| `--plan` | Start in plan mode | Less common in pure automation |
| `--autopilot` | Start in autopilot mode | Useful for more autonomous runs |
| `--max-ai-credits <credits>` | Set AI credit ceiling for the session | Useful in budgeted automation |
| `--max-autopilot-continues <count>` | Limit autopilot continuation messages | Safety and cost control |
| `--no-ask-user` | Disable ask-user questions | Strongly recommended for automation |
| `--enable-memory` | Enable memory in prompt mode | Off by default in prompt mode |
| `--enable-reasoning-summaries` | Request reasoning summaries for OpenAI models | Mostly for debugging or inspection |

### Permissions: tools, paths, and URLs

| Flag | What it does | Headless note |
| --- | --- | --- |
| `--allow-all` | Allow all tools, all paths, and all URLs | Same as `--yolo` |
| `--yolo` | Same as `--allow-all` | Fastest but broadest |
| `--allow-all-tools` | Auto-approve all tools | Often needed for unattended runs |
| `--allow-tool[=tools...]` | Approve specific tools | Best practice for least privilege |
| `--deny-tool[=tools...]` | Deny specific tools | Deny wins over allow |
| `--available-tools[=tools...]` | Only these tools are visible to the model | Visibility filter |
| `--excluded-tools[=tools...]` | Hide these tools from the model | Visibility filter |
| `--allow-all-paths` | Disable path verification | Broad filesystem access |
| `--add-dir <directory>` | Add an allowed directory | Safer than `--allow-all-paths` |
| `--disallow-temp-dir` | Remove automatic temp-dir access | Tightens filesystem access |
| `--allow-all-urls` | Allow all URLs | Broad network access |
| `--allow-url[=urls...]` | Allow specific URLs or domains | Good for docs and APIs |
| `--deny-url[=urls...]` | Deny specific URLs or domains | Deny wins over allow |
| `--allow-all-mcp-server-instructions` | Include init instructions from all MCP servers | Advanced MCP behavior |

### MCP, plugin, and extension flags

| Flag | What it does | Headless note |
| --- | --- | --- |
| `--additional-mcp-config <json>` | Add MCP config inline or from `@file` | Good for ephemeral automation setup |
| `--disable-builtin-mcps` | Disable all built-in MCP servers | Use for a minimal environment |
| `--disable-mcp-server <server-name>` | Disable one MCP server | Repeatable |
| `--add-github-mcp-tool <tool>` | Add specific GitHub MCP tools | Expands the default GitHub MCP subset |
| `--add-github-mcp-toolset <toolset>` | Add specific GitHub MCP toolsets | Expands the default GitHub MCP subset |
| `--enable-all-github-mcp-tools` | Enable all GitHub MCP tools | Broad GitHub MCP access |
| `--plugin-dir <directory>` | Load a plugin from a local directory | Good for custom local automation |
| `--extension-sdk-path <directory>` | Override bundled Copilot SDK for extensions | Mainly for extension development |

### Config, persistence, and environment behavior flags

| Flag | What it does | Headless note |
| --- | --- | --- |
| `--experimental` | Enable experimental features | Can persist the setting |
| `--no-experimental` | Disable experimental features | Useful to force stable behavior |
| `--bash-env[=on\|off]` | Enable `BASH_ENV` support | Can persist |
| `--no-bash-env` | Disable `BASH_ENV` support | Can persist |
| `--no-custom-instructions` | Ignore AGENTS and instructions files | Good for reproducible runs |
| `--no-auto-update` | Disable automatic CLI updates | Good for CI stability |
| `--log-dir <directory>` | Override log directory | Good for capturing artifacts |
| `--log-level <level>` | Set log verbosity | `debug` is useful for failures |
| `--secret-env-vars[=vars...]` | Redact named env var values from output | Important for secure logs |

### Remote and export behavior flags

| Flag | What it does | Headless note |
| --- | --- | --- |
| `--remote` | Enable remote control from GitHub web or mobile | Usually not needed in automation |
| `--no-remote` | Disable remote control | Better default for CI |
| `--remote-export` | Export session to GitHub web or mobile (read-only) | Optional |
| `--no-remote-export` | Disable export to GitHub web or mobile | Better default for isolated runs |

### Output and terminal behavior flags

| Flag | What it does | Headless note |
| --- | --- | --- |
| `--no-color` | Disable ANSI color | Good for logs |
| `--plain-diff` | Disable rich diff rendering | Good for machine logs |
| `--stream <on\|off>` | Enable or disable streaming mode | `off` can make logs simpler |
| `--banner` | Show startup banner | Rarely useful in headless mode |
| `--screen-reader` | Enable screen reader optimizations | Usually not relevant for automation |
| `--mouse[=on\|off]` | Enable mouse support | Mostly interactive |
| `--no-mouse` | Disable mouse support | Mostly interactive |

### Flags that switch to another operating mode

| Flag | What it does | Note |
| --- | --- | --- |
| `--interactive <prompt>` | Start interactive mode and run a prompt | Not headless |
| `--acp` | Start as ACP server | Different operating mode |
| `--help` | Show help | Utility flag |
| `--version` | Show version | Utility flag |

---
