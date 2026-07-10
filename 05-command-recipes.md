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

---
