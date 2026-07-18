# Claude Code — All Topics, Short & Sweet

Quick answers for every major Claude Code concept, each with a one-line definition and example.

---

## 0. The Agentic Loop (core architecture)
**What:** The core cycle Claude Code runs on every task: **gather context → take action → verify results → repeat** until done. Each tool call returns a result that feeds back in and informs the next step.

```
while (Claude requests a tool call):
    execute tool
    feed result back into context
Claude responds with no tool call → loop stops, waits for you
```
**Example:** "Fix the failing tests" → Claude reads the test file (context) → edits the code (action) → reruns tests (verify) → loop continues until tests pass.

**Key terms:**
- **Harness** — Claude Code itself: the file access, shell execution, permission gating, and loop that wraps the model. *"Claude Code is the harness; Claude is the model inside it."*
- **Turn** — one full response from Claude, including any tool calls, from your message to Claude finishing.
- **Session** — the whole run, made of many turns.

---

## 0.1 Built-in Tools
**What:** The actions Claude can take without any setup — this is what makes it "agentic" instead of just a chatbot.

| Category | Examples |
|---|---|
| File operations | Read, Write, Edit |
| Search | Grep, Glob |
| Execution | Bash |
| Web | WebSearch, WebFetch |
| Orchestration | Task (spawn subagent), AskUserQuestion |

**Example:** Ask "what does this project do?" → Claude uses Glob + Read on its own, no manual context-adding needed.

---
**What:** A markdown file at your project root that Claude reads automatically every session — your project's "constitution" (conventions, commands, architecture notes).
**Loads:** Always-on, injected into context at launch.

```markdown
# CLAUDE.md
- Use pnpm, not npm
- Run `pnpm test` before committing
- API routes live in /src/routes
```

---

## 2. Skills (SKILL.md)
**What:** A folder containing a `SKILL.md` file that packages instructions + optional scripts/templates for a specific task. Claude loads it automatically **only when relevant** — so it costs almost nothing the rest of the time.
**Where:** `.claude/skills/` (project) or `~/.claude/skills/` (personal).

```
.claude/skills/pdf-fill/
  SKILL.md
  fill_template.py
```
```yaml
---
name: pdf-fill
description: Fill PDF form fields from a data file. Use when the user wants to fill a PDF form.
---
1. Read the input PDF's field names
2. Map data to fields
3. Run fill_template.py
```
**Example use:** "Use the pdf-fill skill to fill out this W9 with my details."

---

## 3. Hooks
**What:** Shell commands that fire automatically at fixed points in Claude Code's lifecycle (before/after a tool runs, session start, etc.) — deterministic control instead of relying on the model to remember.
**Where:** `.claude/settings.json`

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [{ "type": "command", "command": "npx prettier --write $CLAUDE_FILE_PATH" }]
      }
    ]
  }
}
```
**Example use:** Auto-format every file Claude edits, or block edits to `.env` files (`PreToolUse` + exit code 2 = deny).

**Common events:** `SessionStart`, `PreToolUse`, `PostToolUse`, `UserPromptSubmit`, `Stop`, `Notification`, `SubagentStop`, `PreCompact`.

---

## 4. Subagents
**What:** A separate Claude instance with its own isolated context window, spun up to handle a sub-task (like code exploration) without cluttering your main conversation.
**Where:** `.claude/agents/*.md`

```yaml
---
name: release-notes
description: Writes a release announcement from a changelog.
model: sonnet
---
You are a release communications specialist. Summarize the
changelog into a one-line summary + three highlights.
```
**Example use:** "Use the release-notes subagent to draft this week's changelog summary."

---

## 5. MCP (Model Context Protocol)
**What:** An open protocol that connects Claude to external tools and data sources (databases, APIs, Slack, GitHub) — think "USB-C for AI apps."
**Where:** `.claude/settings.json` → `mcpServers`, or `claude mcp add`.

```bash
claude mcp add github -- npx -y @modelcontextprotocol/server-github
```
**Example use:** "Check my open GitHub issues" — Claude calls the connected GitHub MCP server directly instead of guessing.

---

## 6. Plugins
**What:** The packaging layer — bundles skills, hooks, subagents, and MCP servers into one installable unit you can share with a team via a marketplace.

```bash
/plugin install document-skills@anthropic-agent-skills
```
**Example use:** Install a company-wide plugin that ships your team's lint hooks + release-notes subagent + PDF skill in one command.

---

## 7. Slash Commands
**What:** Explicit, repeatable commands you type inside a session — either built-in (`/clear`, `/help`) or custom ones you define as markdown files.
**Where:** `.claude/commands/*.md` for custom commands.

```markdown
<!-- .claude/commands/deploy.md -->
Run the full deploy checklist:
1. Run tests
2. Build
3. Push to staging
```
**Example use:** Type `/deploy` instead of re-typing the checklist every time.

---

## 8. Permission Modes
**What:** Controls how much Claude can do without asking you first. Cycle with `Shift+Tab`.

| Mode | Behavior |
|---|---|
| **default** | Asks approval before every file change |
| **acceptEdits** | Auto-approves file edits |
| **plan** | Proposes a plan, edits nothing until you approve |

**Example use:** Switch to `plan` mode before a risky refactor so you can review the approach first.

---

## 9. Context Management
**What:** Tools to keep the conversation from getting too bloated for accurate answers.

| Command | What it does |
|---|---|
| `/clear` | Wipes history — start fresh on a new task |
| `/compact` | Summarizes history to free up space, same task |
| `/context` | Visualize current context usage |

**Example use:** Task got long and answers seem to be slipping → check `/context`, then `/compact`.

---

## 10. Git Integration
**What:** Claude Code treats Git operations as natural language requests — no need to memorize git syntax.

```text
what files have I changed?
commit my changes with a descriptive message
create a new branch called feature/login-fix
help me resolve merge conflicts
```

---

## 11. Role / System Prompt
**What:** The instructions that shape *how* Claude behaves — tone, tool rules, safety. Claude Code's default system prompt is set for you, but you can layer a custom "role" on top three ways:

| Method | Effect |
|---|---|
| **Output style** | A saved, reusable markdown file that modifies the system prompt |
| `--append-system-prompt` | Adds extra instructions on top of the default |
| `--system-prompt` | Replaces the default system prompt entirely |

```bash
claude --append-system-prompt "Always write tests before implementation code."
```
**Example use (subagent role):** a subagent's `description` + body *is* its role — e.g., a "release-notes" subagent whose entire persona is "You are a release communications specialist."

---

## 12. Context Window
**What:** The total memory budget for a session — everything Claude can "see" at once: system prompt, CLAUDE.md, file reads, tool outputs, and conversation history.

**What fills it:**
- **Resident (stays loaded):** system prompt, output style, CLAUDE.md, auto memory, skill descriptions, MCP tool names
- **Grows as you work:** file reads, command results, hook outputs
- **Compacted when full:** `/compact` summarizes the conversation to free up space

```text
/context      → see current usage
/compact      → summarize now, on your terms
/compact Keep the auth decisions and current bug details
```
**Example:** Sonnet has a 200K-token window, Opus can run up to 1M — but even that fills up on a long multi-file refactor, which is why `/compact` and subagents (isolating big reads) both exist.

---

## 13. Models & Effort
**What:** Claude Code lets you pick which model powers the session, and (for Opus/Sonnet) how hard it thinks.

```bash
claude --model opus       # deep reasoning: architecture, hard debugging
claude --model sonnet     # default: everyday coding
claude --model haiku      # fast/cheap: renames, lookups, boilerplate
```
```text
/model opus                    # switch mid-session
/effort high                   # more reasoning depth (Opus/Sonnet only)
```
**Example use:** Start on Sonnet, bump to `/model opus` only when you hit a genuinely hard bug — then drop back down to save cost.

---

## 14. Settings Hierarchy
**What:** Where configuration lives, and which wins when they conflict.

| File | Scope |
|---|---|
| `~/.claude/settings.json` | User — applies everywhere |
| `.claude/settings.json` | Project — shared via git |
| `.claude/settings.local.json` | Local only — gitignored |
| CLI flags (`--model`, etc.) | Session only |

**Precedence example:** MCP servers → `local > project > user`. Skills → `managed > user > project`. Hooks are the exception — **all** registered hooks fire regardless of source (they merge, not override).

---

## 15. Agent SDK
**What:** A standalone package that lets you embed Claude Code's same agentic loop into your own applications — no CLI required. You get programmatic control over tools, permissions, and cost limits.

```python
from claude_agent_sdk import query
async for message in query(prompt="Fix the bug in utils.py"):
    print(message)
```
**Example use:** Building a custom bug-fixing bot for your CI pipeline that runs the same loop as Claude Code but with `max_turns` and `max_budget_usd` caps.

---

## Exam-Style Quick Recap

| Term | One-line definition |
|---|---|
| Agentic loop | Gather context → act → verify → repeat until done |
| Harness | Claude Code itself: the wrapper that gives the model tools + a loop |
| Turn | One Claude response, start to finish, including tool calls |
| Session | The full run — many turns |
| CLAUDE.md | Always-on project memory file |
| Skill / SKILL.md | On-demand folder of instructions, loaded only when relevant |
| Hook | Deterministic shell command fired at a lifecycle event |
| Subagent | Isolated-context Claude instance for a sub-task |
| MCP | Protocol connecting Claude to external tools/data |
| Plugin | Packaging unit bundling skills/hooks/agents/MCP for sharing |
| Slash command | Explicit repeatable command, built-in or custom |
| Permission mode | default / acceptEdits / plan — how much Claude can do unprompted |
| Context window | The token budget the whole session shares |
| Compaction (`/compact`) | Summarizes history to free up context window space |
| Output style | Saved file that persistently modifies the system prompt |
| Effort level | How hard Opus/Sonnet reasons (low → xhigh) |
| Settings hierarchy | user < project < local < CLI flag (hooks merge instead) |
| Agent SDK | Embed the same agentic loop in your own app |

---

## How the Layers Fit Together

| Layer | Solves |
|---|---|
| **CLAUDE.md** | Always-on project context |
| **Skills** | On-demand expertise, loaded only when needed |
| **Hooks** | Deterministic automation/guardrails |
| **Subagents** | Isolated context for sub-tasks |
| **MCP** | Connecting external tools/data |
| **Plugins** | Packaging & sharing all of the above |
| **Role / System Prompt** | Persistent behavior/tone changes |
| **Context Window** | The budget everything above shares |
| **Models & Effort** | Speed/cost vs. reasoning depth trade-off |

**Rule of thumb:** Start with CLAUDE.md for conventions → add a Skill when you catch yourself repeating a workflow → add a Hook when you want a rule enforced automatically, not just suggested → reach for MCP when you need live external data.

---

*Sources: [code.claude.com/docs](https://code.claude.com/docs/en/features-overview) — official Claude Code documentation*
