# Claude Code — Complete Slash Command Reference

A full reference of Claude Code's `/` commands: official/public commands, plus community-documented, leak-based, internal, and removed ones. Status labels tell you how reliable each entry is.

**Status key:**
- 🟢 **Public** — documented/stable, safe to rely on
- 🔵 **Public alias/shortcut** — works, but is a shortcut into another command
- 🟡 **Community** — works via widely-shared custom command conventions, not built into core Claude Code
- ⚪ **Built-in skill command** — ships as a bundled skill, invoked like a slash command
- ⚫ **Leak-based / Internal** — surfaced in changelogs, issue trackers, or binaries; not officially documented, may not exist in every build, can change or disappear without notice
- 🔴 **Removed** — no longer works, listed for historical reference

Always run `/help` in your own session for the ground-truth list — it reflects your installed version plus any custom, plugin, and MCP-provided commands.

Sources: [Anthropic's official Claude Code cheatsheet](https://support.claude.com/en/articles/14553413-claude-code-cheatsheet), [Anthropic slash command docs](https://platform.claude.com/docs/en/agent-sdk/slash-commands), GitHub `anthropics/claude-code` issues/changelog, and a community-audited cheatsheet (jqueryscript/Claude-Code-Slash-Commands-Cheatsheet, audited Aug 2026).

---

## 1. Session & Context Management

| Command | What it does | Status |
|---|---|---|
| `/clear` | Wipe conversation history and start fresh (project memory stays). Aliases: `/reset`, `/new` | 🟢 |
| `/compact [instructions]` | Summarize conversation history to free up context; optional focus instructions | 🟢 |
| `/context` | Visualize current context usage as a colored grid with token counts/percentages | 🟢 |
| `/rewind` | Roll conversation and/or code back to an earlier checkpoint, including before `/clear`. Alias: `/undo` | 🟢 |
| `/resume [session]` | Reopen a previous session by ID or name, or open the session picker. Alias: `/continue` | 🟢 |
| `/rename [name]` | Rename the current session; auto-generates one if left blank | 🟢 |
| `/fork [name]` / `/branch [name]` | Branch the current conversation into a new session from this point | 🟢 |
| `/cd <path>` | Move the current session to a new working directory without breaking prompt cache | 🟢 |
| `/add-dir <path>` | Grant Claude file access to an additional working directory | 🟢 |
| `/export [filename]` | Save the conversation to a file or clipboard | 🟢 |
| `/copy [N]` | Copy the latest (or a selected) response/code block to clipboard | 🟢 |
| `/btw <question>` | Ask a quick side question without adding it to the main conversation or consuming context | 🟢 |
| `/goal` | Set a completion condition and have Claude keep working across turns until it's met | 🟢 |
| `/status` | Show account, model, working directory, and version | 🟢 |
| `/exit` | Quit the CLI. Alias: `/quit` | 🟢 |
| `/session` | Session management UI | ⚫ |
| `/share` | Share a session | ⚫ |
| `/summary` | Generate a session summary | ⚫ |

---

## 2. Model, Effort & Planning

| Command | What it does | Status |
|---|---|---|
| `/model [model]` | View or switch the active model (Haiku/Sonnet/Opus/Fable). Picker saves default for new sessions; `s` switches only current session | 🟢 |
| `/effort [low\|medium\|high\|xhigh\|max\|auto]` | Set reasoning effort level; no argument opens an interactive slider | 🟢 |
| `/fast [on\|off]` | Toggle fast mode on supported Opus models | 🟢 |
| `/plan [description]` | Enter read-only Plan Mode where Claude proposes an approach without editing/running anything | 🟢 |

---

## 3. Project & Memory

| Command | What it does | Status |
|---|---|---|
| `/init` | Explore the codebase and generate a starter `CLAUDE.md` | 🟢 |
| `/memory` | View or edit the `CLAUDE.md` memory files (user, project, local scope) | 🟢 |
| `/team-onboarding` | Generate a teammate ramp-up guide from local Claude Code usage | 🟢 |

---

## 4. Configuration & UI

| Command | What it does | Status |
|---|---|---|
| `/config` | Open configuration settings (theme, defaults, editor mode); `/config key=value` sets directly. Alias: `/settings` | 🟢 |
| `/permissions` | View or update which tools require approval; review recent auto-mode denial reasons | 🟢 |
| `/sandbox` | Open sandbox controls — block credential reads, remember approved network hosts, etc. | 🟢 |
| `/keybindings` | Edit keyboard shortcuts, including chord sequences | 🟢 |
| `/theme` | Change or create color themes, including custom JSON/plugin-shipped themes | 🟢 |
| `/color [color\|default]` | Set the prompt bar accent color for the current session | 🟢 |
| `/statusline` | Configure a custom status line | 🟢 |
| `/scroll-speed` | Tune mouse-wheel scroll speed with live preview | 🟢 |
| `/tui [fullscreen]` | Switch terminal UI rendering mode | 🟢 |
| `/terminal-setup` | Install terminal keybindings (e.g. shift+enter for newlines in IDE terminals) | 🟢 |
| `/output-style [style]` | Change the response style (e.g. "Concise") | 🟢 |
| `/privacy-settings` | Manage privacy/telemetry preferences | ⚫ |
| `/vim` | *(Removed in v2.1.92)* — vim-mode is now a setting under `/config` | 🔴 |

---

## 5. Cost, Usage & Diagnostics

| Command | What it does | Status |
|---|---|---|
| `/usage` | Show plan usage limits, current rate-limit status, quota and cost breakdowns | 🟢 |
| `/cost` | Token usage/spend for the session — now a shortcut into `/usage` | 🔵 |
| `/stats` | Daily usage, session history, streaks, model preferences — shortcut into `/usage` | 🔵 |
| `/usage-credits` | Pay-as-you-go overflow billing info for subscription plans (was `/extra-usage`, kept as alias) | 🟢 |
| `/doctor` | Diagnose Claude Code install/environment issues. Alias: `/checkup` | 🟢 |
| `/insights` | Show usage/session insights | 🟢 |
| `/feedback` | Report an issue to Anthropic with session context attached. Alias: `/bug` | 🟢 |
| `/release-notes` | View the Claude Code changelog | 🟢 |
| `/powerup` | Open interactive in-CLI lessons on Claude Code features | 🟢 |
| `/version` | Show installed Claude Code version | ⚫ |
| `/upgrade` | Open the plan-upgrade page | ⚫ |

---

## 6. Code Review, Debugging & Workflows

| Command | What it does | Status |
|---|---|---|
| `/review` | Review current code changes (PR-review mode uses the same engine as `/code-review medium`) | 🟢 |
| `/code-review [effort]` | Review for correctness issues; supports `--fix` or `--comment` flags | 🟢 |
| `/security-review` | Run a security-focused vulnerability review | 🟢 |
| `/simplify` | Bundled skill: review recently changed files for reuse/quality/efficiency issues and apply fixes | 🟢 |
| `/diff` | Open an interactive viewer of uncommitted changes and per-turn diffs | 🟢 |
| `/debug [description]` | Run a structured debugging workflow | 🟢 |
| `/loop <interval> <prompt>` | Run a prompt or slash command on a recurring interval (e.g. `/loop 5m check the deploy`). Alias: `/proactive` | 🟢 |
| `/batch` | Apply one change across many files or worktrees | 🟢 |
| `/ultrareview [PR#]` | Comprehensive cloud code review with parallel multi-agent analysis | 🟢 |
| `/focus` | Toggle Focus view | 🟢 |
| `/workflows` | View dynamic (`ultracode`-triggered) workflow runs | 🟢 |
| `/pr-comments [PR]` | Fetch PR review comments. Internal underscore form: `/pr_comments` | 🟢 |
| `/install-github-app` | Set up GitHub App integration (Actions workflow setup optional) | 🟢 |
| `/less-permission-prompts` | Scan transcripts for safe read-only Bash/MCP allowlist candidates | ⚪ |
| `/ultraplan` | Detailed planning workflow | ⚫ |
| `/tasks` | Manage background tasks | ⚫ |

---

## 7. Extensions: MCP, Plugins, Skills, Agents

| Command | What it does | Status |
|---|---|---|
| `/mcp` | Manage MCP server connections and OAuth authentication | 🟢 |
| `/mcp__[server]__[prompt] [args]` | Run a dynamic prompt command exposed by a connected MCP server | 🟢 (generated) |
| `/agents` | List, create, or edit subagents for specialized tasks | 🟢 |
| `/skills` | List skills available in this session | 🟢 |
| `/reload-skills` | Re-scan skill directories without restarting Claude Code | 🟢 |
| `/plugin` | Manage plugins; inspect components; follow marketplace renames automatically | 🟢 |
| `/plugin list [--enabled\|--disabled]` | List installed plugins | 🟢 |
| `/reload-plugins` | Reload plugins (works from Remote Control clients too) | 🟢 |
| `/hooks` | View/configure hook scripts for tool events (SessionStart, PostToolUse, etc.) | 🟢 |
| `/claude-api` | Built-in skill: Claude API/SDK helper workflow | ⚪ |

---

## 8. Remote & Cross-Device

| Command | What it does | Status |
|---|---|---|
| `/remote-control` | Start/manage Remote Control. Short form: `/rc` | 🟢 |
| `/remote-env` | Configure remote environments | 🟢 |
| `/teleport` | Bridge/transfer sessions across devices; resume a remote claude.ai session (subscribers) | 🟢 |
| `/schedule` | Manage cloud scheduled tasks | 🟢 |
| `/ide` | Open or manage IDE integration | 🟢 |
| `/chrome` | Open Chrome browsing integration (availability varies) | 🟢 |
| `/desktop` | Hand off the session to the Claude Desktop app | 🟢 |
| `/voice` | Enable push-to-talk voice mode | 🟢 |
| `/mobile` | Mobile integration/handoff | ⚫ |
| `/oauth-refresh` | Refresh OAuth tokens | ⚫ |

---

## 9. Authentication

| Command | What it does | Status |
|---|---|---|
| `/login` | Authenticate / switch Anthropic accounts | 🟢 |
| `/logout` | Sign out of the current account | 🟢 |

---

## 10. Community-Convention Commands (not built-in — user/team-defined)

These aren't shipped with Claude Code; they're extremely common custom commands teams add via `.claude/commands/` or `.claude/skills/`. They only work if someone has defined them in your project.

| Command | Typical purpose |
|---|---|
| `/commit` | Generate a commit message and commit staged changes |
| `/commit-push-pr` | Commit, push, and open a PR in one step |
| `/pr` | Create a pull request |
| `/push` | Push the current branch |
| `/lint` | Run project lint commands |
| `/merge-to-main` | Merge current branch to main |
| `/fix-pipeline` | Repair a failing CI pipeline |
| `/vitest` | Run Vitest-based test workflows |

---

## 11. Internal / Leak-Based / Easter-Egg (unreliable — informational only)

Surfaced via changelogs, GitHub issues, or binary inspection. Not documented, may not exist in your build, and can vanish without notice. Included only for completeness — don't build workflows around these.

`/advisor` · `/ant-trace` · `/autofix-pr` · `/backfill-sessions` · `/brief` · `/bridge` · `/bridge-kick` · `/bughunter` · `/buddy` (Apr-1 joke) · `/ctx_viz` · `/debug-tool-call` · `/env` · `/files` · `/good-claude` (easter egg) · `/heapdump` · `/init-verifiers` · `/install` · `/issue` · `/mock-limits` · `/onboarding` · `/passes` · `/perf-issue` · `/rate-limit-options` · `/remote-setup` · `/reset-limits` · `/sandbox-toggle` · `/stickers` (merch easter egg) · `/thinkback` · `/thinkback-play` · `/x402`

**Removed:** `/tag` (removed v2.1.92)

---

## 12. Keyboard Shortcuts (not slash commands, but essential)

| Key | Action |
|---|---|
| **Shift+Tab** | Cycle permission mode: auto → manual → acceptEdits → plan (→ bypassPermissions if enabled) |
| **Esc** | Interrupt Claude mid-response |
| **Esc, Esc** | Open the rewind/checkpoint menu |
| **Ctrl+C** | Cancel input, or exit on empty prompt |
| **Ctrl+R** | Reverse search through prompt history |
| **Ctrl+O** | Expand to full verbose transcript view |
| **↑ / ↓** | Scroll prompt history |
| **@ + path** | Reference a file/directory in your prompt |
| **/** | Open the command menu |
| **!** | Bash mode — run a shell command directly |
| **#** | Quick-add a note to CLAUDE.md memory |
| **?** | Show shortcuts for your current terminal/IDE |

---

## 13. Quick Reference: CLI Commands & Flags (launch-time, not in-session)

### Core commands

| Command | Purpose |
|---|---|
| `claude` | Start interactive session in current directory |
| `claude "prompt"` | Start with an initial prompt |
| `claude -p "prompt"` | Print mode — single response, then exit (for scripts/CI) |
| `claude -c` / `--continue` | Continue the most recent conversation |
| `claude -r [name]` | Resume a session by ID/name, or open the picker |
| `claude update` | Update Claude Code to the latest version |
| `claude agents` | Open agent view: running, blocked, and completed sessions |
| `claude agents --json` | List active sessions as JSON (`id`, `state` fields) |
| `claude agents --json --all` | Include completed sessions in the JSON agent list |
| `claude agents` then `! <command>` | Start a shell command as a background session you can attach/detach from |
| `claude --bg --exec '<command>'` | Run a shell command as a background Claude session |
| `claude mcp list` | List configured MCP servers |
| `claude mcp login <name>` | Authenticate an MCP server from the CLI (`--no-browser` for SSH/headless) |
| `claude mcp logout <name>` | Sign out an MCP server from the CLI |
| `claude mcp serve` | Run Claude Code itself as an MCP server |
| `claude plugin init <name>` | Scaffold a new plugin in `.claude/skills` |
| `claude plugin details <name>` | Show plugin components and projected per-session token cost |
| `claude plugin tag` | Create release git tags for a plugin, with version validation |
| `claude plugin marketplace remove <name> --scope user\|project\|local` | Remove a marketplace from a given config scope |

### Flags

| Flag | Purpose |
|---|---|
| `--add-dir` | Add an extra directory to scope |
| `--agent` | Select a specific agent |
| `--allowedTools` | Pre-approve tools so they don't prompt mid-run |
| `--bare` | Skip auto-discovery of hooks, skills, plugins, MCP servers, auto-memory, and `CLAUDE.md` for a fast, reproducible run. See examples below. Sets `CLAUDE_CODE_SIMPLE=1` |
| `--channels` | Enable channel/MCP push relay |
| `--chrome` | Enable Chrome integration mode |
| `--console` | Use Anthropic Console auth instead of claude.ai |
| `--dangerously-skip-permissions` | Skip all permission prompts (sandboxed/CI use only) |
| `--effort` | Set reasoning effort at launch |
| `--exclude-dynamic-system-prompt-sections` | Improve print-mode cross-user prompt caching |
| `--fallback-model` | Continue with a configured fallback model if the primary is unavailable |
| `--json-schema` | Request structured output matching a schema |
| `--max-budget-usd` | Cap spend for the session |
| `--max-turns` | Limit the number of internal tool-use turns |
| `--mcp-config` | Use a specific MCP config for dispatched background sessions |
| `--model` | Set the model for the session |
| `-n <name>` / `--name <name>` | Name the session at launch, for finding/resuming it by name later |
| `--output-format <text\|json\|stream-json>` | Set machine-readable output format |
| `--permission-mode <mode>` | Set permission behavior (`acceptEdits`, `plan`, etc.) |
| `--plugin-dir` | Use a plugin directory for dispatched background sessions |
| `--plugin-url <url>` | Fetch a plugin `.zip` archive from a URL for the current session |
| `-r [session]` | Resume a session |
| `--remote` | Start a remote/web-backed session |
| `--remote-control-session-name-prefix` | Prefix Remote Control session names |
| `--safe-mode` | Start with customizations disabled, for troubleshooting |
| `--settings` | Load specific settings for dispatched background sessions |
| `--tools` | Explicitly allow specific tools |
| `--transport http\|stdio\|sse` | Select MCP transport protocol |
| `--verbose` | Enable verbose output |
| `-w` / `--worktree` | Use a git worktree for the session |

### `--bare` in detail

Without `--bare`, `claude -p` loads the same context an interactive session would: hooks/skills/plugins from `~/.claude`, MCP servers from `.mcp.json`, auto-memory/`CLAUDE.md` discovery walking up from the cwd, and keychain/OAuth lookups. `--bare` skips all of it, so the only inputs are the flags you pass explicitly — a reproducible black box, good for CI where you don't want a teammate's global hook or a stray `CLAUDE.md` silently changing behavior. Anthropic's docs note it's the recommended mode for scripted/SDK calls and expected to become the default for `-p` in a future release. Bash, file-read, and file-edit tools are still available by default in bare mode.

```bash
# Basic bare call, pre-approving the Read tool
claude --bare -p "Summarize this file" --allowedTools "Read"

# Fast commit message generation — no need for hooks/LSP/plugin sync
git diff --staged | claude --bare -p "write a one-line commit message for this diff"

# Self-contained CI step
claude --bare -p "run the test suite and summarize failures" \
  --allowedTools "Bash,Read" \
  --output-format json

# Passing back in what bare mode drops (system prompt, settings)
claude --bare -p "review this PR diff for security issues" \
  --append-system-prompt "Follow OWASP top 10 conventions." \
  --allowedTools "Read"

# Piped + bare, for a deterministic CI gate
cat build-error.txt | claude --bare -p "concisely explain the root cause" > output.txt
```

| To load back in | Use |
|---|---|
| System prompt additions | `--append-system-prompt`, `--append-system-prompt-file` |
| Settings | `--settings <file-or-json>` |

---

## 14. `claude -p` — Print Mode (for scripts, CI, piping)

`-p` runs Claude once, prints the final result, and exits — no interactive session, no TUI. This is what makes `claude` scriptable and pipe-friendly.

```bash
claude -p "explain what this function does" < utils.py
echo "some text" | claude -p "translate to French"
```

- Reads stdin if piped in
- Prints only the final response (no tool-call chatter) unless verbose/streaming output is requested
- Exits with a normal status code, so it works with `&&`, `|`, `;` in shell and CI

### Useful flags with `-p`

| Flag | Purpose |
|---|---|
| `--output-format <text\|json\|stream-json>` | Machine-readable output for parsing in scripts |
| `--model <name>` | Pin the model for this run |
| `--max-turns <n>` | Cap how many internal tool-use turns it can take |
| `--permission-mode <mode>` | Control approval behavior (e.g. `acceptEdits`, `plan`) |
| `--dangerously-skip-permissions` | Skip all prompts — sandboxed/CI use only |
| `--allowedTools` | Pre-approve specific tools so it doesn't need permission mid-run |
| `--max-budget-usd` | Cap spend for the run |
| `--json-schema` | Force structured JSON output matching a schema |

---

## 15. Piping INTO `claude`

```bash
# Pipe file contents
cat app.py | claude -p "review this code for bugs"
< app.py claude -p "review this code for bugs"

# Pipe command output
git diff | claude -p "write a commit message for this diff"
git log --oneline -20 | claude -p "summarize recent changes"

# Pipe error/log output
npm test 2>&1 | claude -p "explain why these tests are failing"
docker logs mycontainer | claude -p "find the root cause of this crash"

# Pipe curl/API output
curl -s https://api.example.com/data | claude -p "explain this JSON schema"

# Pipe multiple files
cat *.py | claude -p "find duplicate logic across these files"
```

---

## 16. Piping OUT of `claude`

```bash
# Save output to a file
claude -p "generate a README for this project" > README.md

# Pipe into grep/filter
claude -p "list all TODOs in this repo" | grep -i "urgent"

# Pipe into jq (with structured output)
claude -p "list API endpoints as JSON" --output-format json < routes.py | jq '.endpoints'

# Pipe into another CLI tool
claude -p "write a haiku about deployment" | cowsay
claude -p "draft a Slack message" | curl -X POST -d @- "$SLACK_WEBHOOK_URL"

# Pipe into xargs for batch actions
claude -p "list files that need deleting, one per line" | xargs rm
```

---

## 17. Chaining `claude` → `claude`

```bash
# Two-step pipeline: summarize, then reformat
git diff | claude -p "summarize this diff" | claude -p "turn this into a changelog entry"

# Save each intermediate step
claude -p "summarize app.py" < app.py > summary.txt
claude -p "turn this summary into a changelog entry" < summary.txt

# Combine with tee to see AND save output mid-chain
cat error.log | claude -p "diagnose this error" | tee diagnosis.txt
```

---

## 18. Scripted / CI Pipelines

```bash
# Simple pass/fail chain
claude -p "run tests and report failures" && echo "clean" || echo "issues found"

# Conditional gate in a pre-commit or CI step
git diff --staged | claude -p "any security issues in this diff? reply YES or NO plus reason" | \
  grep -q "^YES" && echo "Blocked: security concerns found" && exit 1

# Multi-step pipeline: analyze diff, draft alert, post to Slack
#!/bin/bash
DIFF=$(git diff main)
RISKS=$(echo "$DIFF" | claude -p "list security risks in this diff")
if [ -n "$RISKS" ]; then
  echo "$RISKS" | claude -p "draft a Slack message summarizing these risks" | \
    curl -X POST -d @- "$SLACK_WEBHOOK_URL"
fi
```

### Notes on piping & chaining

- `-p` (print mode) is what enables piping — without it, `claude` starts an interactive session and won't treat stdin as a one-shot command
- Use `--output-format json` or `stream-json` when parsing output programmatically (`jq`, etc.) instead of scraping plain text
- Large piped input still counts against context — for big files, consider letting Claude read the file itself via `@filename` reference in an interactive session rather than piping the whole thing through `-p`
- Exit codes behave normally, so `&&`, `||`, and `;` chaining works as expected in scripts and CI

---

## 19. Environment Variables

Launch-time configuration via env vars — set these in your shell profile or CI config, not inside a session.

| Variable | What it does |
|---|---|
| `CLAUDE_CODE_CERT_STORE=bundled` | Use bundled CAs only instead of the OS certificate store |
| `CLAUDE_CODE_DISABLE_BUNDLED_SKILLS` | Hide bundled skills, workflows, and built-in slash commands from the model |
| `CLAUDE_CODE_ENABLE_AUTO_MODE=1` | Enable Auto mode on Bedrock/Vertex/Foundry for supported Opus setups |
| `CLAUDE_CODE_ENABLE_FEEDBACK_SURVEY_FOR_OTEL` | Re-enable the session quality survey for OpenTelemetry-based enterprises |
| `CLAUDE_CODE_DISABLE_BG_SHELL_PRESSURE_REAP=1` | Disable automatic memory-pressure cleanup for idle background shell commands |
| `CLAUDE_CODE_MCP_TOOL_IDLE_TIMEOUT` | Override the idle timeout for unresponsive remote MCP tool calls |
| `CLAUDE_CLIENT_PRESENCE_FILE` | Point to a local marker file that suppresses mobile push notifications while you're at the machine |
| `CLAUDE_CODE_PERFORCE_MODE=1` | Make Edit/Write/NotebookEdit fail on read-only Perforce files with a `p4 edit` hint |
| `CLAUDE_CODE_SAFE_MODE` | Start with customizations disabled, for troubleshooting |
| `CLAUDE_CODE_SCRIPT_CAPS` | Limit per-session script invocations |
| `CLAUDE_CODE_USE_MANTLE=1` | Enable Amazon Bedrock powered by Mantle |
| `DISABLE_UPDATES` | Block all update paths, including manual `claude update` (stricter than `DISABLE_AUTOUPDATER`) |
| `CLAUDE_CODE_SESSION_ID` | Set/read the session ID |
| `CLAUDE_CODE_USE_POWERSHELL_TOOL` | Opt into/out of the PowerShell tool; set `1` on Linux/macOS when `pwsh` is available |
| `CLAUDE_CODE_FORCE_SYNC_OUTPUT=1` | Force synchronized output on terminals where auto-detection misses it (e.g. Emacs eat) |
| `CLAUDE_CODE_DISABLE_ALTERNATE_SCREEN=1` | Opt out of the fullscreen alternate-screen renderer, keep output in native scrollback |
| `CLAUDE_CODE_PACKAGE_MANAGER_AUTO_UPDATE` | Let Homebrew/WinGet installs run package-manager upgrades in the background |
| `CLAUDE_CODE_ENABLE_GATEWAY_MODEL_DISCOVERY=1` | Enable gateway `/v1/models` discovery for the `/model` picker |
| `OTEL_LOG_ASSISTANT_RESPONSES` | Control assistant response text in OpenTelemetry logs (`0` = prompts-only, `1` = log response content) |
| `OTEL_LOG_TOOL_DETAILS=1` | Add tool parameters (Bash commands, MCP/skill names) to tool decision telemetry events |
| `OTEL_RESOURCE_ATTRIBUTES` | Add custom labels (team, repo, etc.) to OpenTelemetry metric datapoints |
| `CLAUDE_CODE_MAX_RETRIES` | Cap retry attempts (max 15) |
| `CLAUDE_CODE_RETRY_WATCHDOG` | Enable unattended retry monitoring |

> **Note:** Remote Control, `/schedule`, claude.ai MCP connectors, and notification preferences are disabled when `ANTHROPIC_API_KEY`, `apiKeyHelper`, or `ANTHROPIC_AUTH_TOKEN` is set.

---

*Compiled September 2026. Built-in commands (🟢/🔵) reflect Anthropic's official documentation; everything else is community/changelog-sourced and may vary by version. Always confirm with `/help` in your own installed version.*
