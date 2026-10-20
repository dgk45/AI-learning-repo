# AI CLI Cheat Sheet

Quick reference for **Claude Code, Codex CLI, GitHub Copilot CLI, Gemini CLI, and AGY**.

## 1. Main CLI Commands

| Action | Claude Code | Codex CLI | Copilot CLI | Gemini CLI | AGY |
|---|---|---|---|---|---|
| 🚀 Start | `claude` | `codex` | `copilot` | `gemini` | `agy` |
| 🔄 Continue last session | `claude -c` | `codex resume --last` | `copilot --continue` | `gemini --resume` | `agy -c` |
| 📂 Resume/select session | `claude -r` | `codex resume` | `copilot --resume` | `gemini --resume` | `agy --conversation <ID>` |
| ❓ Help | `claude --help` | `codex --help` | `copilot --help` | `gemini --help` | `agy --help` |
| 🔢 Version | `claude --version` | `codex --version` | `copilot --version` | `gemini --version` | `agy --version` |
| 💬 Prompt directly | `claude "prompt"` | `codex "prompt"` | `copilot -p "prompt"` | `gemini -p "prompt"` | `agy -p "prompt"` |
| ⚡ Non-interactive | `claude -p "prompt"` | `codex exec "prompt"` | `copilot -p "prompt"` | `gemini -p "prompt"` | `agy -p "prompt"` |

## 2. Continue / Resume — Most Important

```text
Claude      → claude -c
Codex       → codex resume --last
Copilot     → copilot --continue
Gemini      → gemini --resume
AGY         → agy -c
```

## 3. Interactive Commands

These commands are used **inside the AI CLI session**.

| Function | Claude | Codex | Copilot | Gemini | AGY |
|---|---|---|---|---|---|
| Help | `/help` | `/help` | `/help` | `/help` | `?` |
| Resume | `/resume` | `/resume` | `/resume` | `/resume` | `/resume` |
| Clear / new context | `/clear` | `/clear` | `/clear` | `/clear` | `/clear` |
| Exit | `/exit` | `/exit` | `/exit` | `/quit` | `Ctrl+D` |
| Status / usage | `/status` | `/status` | `/usage` | `/stats` | — |
| Rewind / undo | `/rewind` | — | — | — | `/rewind` |

## 4. Quick Start

### Claude Code

```bash
claude
```

Continue the latest conversation:

```bash
claude -c
```

### Codex CLI

```bash
codex
```

Continue the latest session:

```bash
codex resume --last
```

### GitHub Copilot CLI

```bash
copilot
```

Continue the latest session:

```bash
copilot --continue
```

### Gemini CLI

```bash
gemini
```

Resume a previous session:

```bash
gemini --resume
```

### AGY

```bash
agy
```

Continue the latest conversation:

```bash
agy -c
```

## 5. Prompt Directly

You can start an agent and give it a prompt from the command line.

### Claude

```bash
claude "Explain this project"
```

### Codex

```bash
codex "Explain this project"
```

### Copilot

```bash
copilot -p "Explain this project"
```

### Gemini

```bash
gemini -p "Explain this project"
```

### AGY

```bash
agy -p "Explain this project"
```

## 6. Version Check

Use these commands to check which version is installed:

```bash
claude --version
codex --version
copilot --version
gemini --version
agy --version
```

## 7. Help

If a command does not work, check the installed CLI's own help first:

```bash
claude --help
codex --help
copilot --help
gemini --help
agy --help
```

> **Note:** CLI commands and flags can change between versions. In particular, GitHub Copilot CLI commands may differ depending on the installed version. Always use `<cli> --help` to verify commands for your installed version.

## 8. Recommended Learning Setup

For learning AI coding agents with one large project, these five CLIs make a useful comparison:

1. **Claude Code**
2. **Codex CLI**
3. **GitHub Copilot CLI**
4. **Gemini CLI**
5. **AGY**

Use the **same project** with each CLI and compare:

```text
Planning
   ↓
Code generation
   ↓
File editing
   ↓
Testing
   ↓
Debugging
   ↓
Refactoring
   ↓
Git operations
   ↓
MCP
   ↓
Agents / subagents
   ↓
Long-context project understanding
```
