# Claude Code Cheat Sheet

## Install & Start

**macOS / Linux / WSL**
```bash
curl -fsSL https://claude.ai/install.sh | bash
```

**Windows PowerShell**
```powershell
irm https://claude.ai/install.ps1 | iex
```

**Windows CMD**
```batch
curl -fsSL https://claude.ai/install.cmd -o install.cmd && install.cmd && del install.cmd
```

**Verify install**
```bash
claude --version
```

**Start a session**
```bash
cd /path/to/your/project
claude
```

---

## Shell Commands (run from your terminal)

| Command | What it does | Example |
|---|---|---|
| `claude` | Start interactive mode | `claude` |
| `claude "task"` | Run a one-time task | `claude "fix the build error"` |
| `claude -p "query"` | Run one-off query, then exit | `claude -p "explain this function"` |
| `claude -c` | Continue most recent conversation in current directory | `claude -c` |
| `claude -r` | Resume a previous conversation | `claude -r` |

---

## Session Commands (used inside Claude Code)

| Command | What it does |
|---|---|
| `/help` | Show available commands |
| `/clear` | Clear conversation history |
| `/compact` | Summarize history to free up context |
| `/login` | Log in or switch accounts |
| `/resume` | Continue a previous conversation |
| `/exit` (or Ctrl+D twice) | Exit Claude Code |

---

## Keyboard Shortcuts

- `Shift+Tab` — cycle permission modes (default → auto-accept edits → plan mode)
- `/` — browse all commands and skills
- `Tab` — command completion
- `↑` — command history

---

## Permission Modes

- **Default** — Claude asks for approval before each file change
- **acceptEdits** — auto-approves file edits
- **plan** — Claude proposes changes without editing files

---

## Everyday Prompts That Work Well

**Understand a codebase**
```
what does this project do?
what technologies does this project use?
where is the main entry point?
explain the folder structure
```

**Make changes**
```
add a hello world function to the main file
refactor the authentication module to use async/await instead of callbacks
```

**Git**
```
what files have I changed?
commit my changes with a descriptive message
create a new branch called feature/quickstart
show me the last 5 commits
help me resolve merge conflicts
```

**Testing & docs**
```
write unit tests for the calculator functions
update the README with installation instructions
review my changes and suggest improvements
```

**Debugging**
```
there's a bug where users can submit empty forms - fix it
```

---

## Pro Tips

- Be specific: "fix the login bug where users see a blank screen after entering wrong credentials" beats "fix the bug"
- Break complex tasks into numbered steps
- Let Claude explore the code first before asking it to make changes
- Use `/clear` when switching tasks; use `/compact` to shrink a long conversation on the same task

---

*Source: [Claude Code Quickstart](https://code.claude.com/docs/en/quickstart) — Anthropic official docs*
