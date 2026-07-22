# Command translation recipes (copy/paste)

```bash
# 1. Quick one-shot in a script
claude -p "summarize the failing tests"     # Claude
copilot -p "summarize the failing tests"    # Copilot

# 2. Pipe a file in headless
cat error.log | copilot -p "explain the root cause and suggest a fix"

# 3. Compose with any Unix tool (the terminal is the API)
git diff | copilot -p "review this diff for security issues"
copilot -p "generate release notes" < commits.txt

# 4. Unattended pipeline step (trust all tools) — use inside sandbox/CI
copilot -p "apply the migration and run tests" --allow-all-tools

# 5. Granular tool trust instead of full bypass
copilot -p "clean up branches" --allow-tool 'shell(git *)' --deny-tool 'shell(git push *)'

# 6. Pick a model per step (cost tuning)
copilot -p "draft the refactor" --model claude-sonnet-5
copilot -p "final review pass"  --model gpt-5.6

# 7. Run as a named custom agent
copilot --agent=reviewer -p "review the diff on this branch"

# 8. Continue / resume a session
copilot --continue
copilot --resume            # opens session picker

# 9. Add extra working directories
copilot --add-dir ../shared-lib --add-dir ../protos

# 10. Cloud (offload a long autonomous run)
copilot --cloud "implement the feature in issue #482 and open a PR"
```

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

## Generic headless setup with a dedicated settings file

For CI systems or wrapper scripts, you can point Copilot at a dedicated config
directory with `COPILOT_HOME` and place a `settings.json` there.

Settings file locations:

- User/global: `~/.copilot/settings.json`
- Repo-shared: `.github/copilot/settings.json`
- Repo-local override: `.github/copilot/settings.local.json`
- Alternate global location: `$COPILOT_HOME/settings.json`

Example `settings.json` for non-interactive runs:

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

Generic shell setup:

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

---
