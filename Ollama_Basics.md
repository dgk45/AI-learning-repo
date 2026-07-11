# Ollama Basics

> Beginner's guide to Ollama for GitHub Copilot Certification Practice

---

# Table of Contents

1. Check Version
2. Install Models
3. List Models
4. Run a Model
5. Exit Chat
6. Show Model Information
7. Running Models
8. Stop a Model
9. Remove a Model
10. Update a Model
11. Start Ollama Server
12. Help
13. Use Ollama API
14. Most Used Commands

---

# 1. Check Ollama Version

```bash
ollama --version
```

Example:

```
ollama version 0.10.x
```

---

# 2. Download (Pull) a Model

Download Qwen3:

```bash
ollama pull qwen3:8b
```

Download Llama:

```bash
ollama pull llama3.1:8b
```

Download Gemma:

```bash
ollama pull gemma3:12b
```

---

# 3. List Installed Models

```bash
ollama list
```

Example:

```
NAME                SIZE
qwen3:8b            5.2 GB
llama3.1:8b         4.9 GB
gemma3:12b          8.1 GB
```

---

# 4. Run (Chat with) a Model

```bash
ollama run qwen3:8b
```

Example:

```
> Explain SOLID principles.
```

---

# 5. Exit Chat

Type:

```
/bye
```

or press

```
Ctrl + D
```

---

# 6. Show Model Information

```bash
ollama show qwen3:8b
```

Displays:

- Model family
- Parameters
- Context size
- Quantization
- License

---

# 7. Show Running Models

```bash
ollama ps
```

Example:

```
NAME        PROCESSOR
qwen3:8b    CPU
```

---

# 8. Stop a Running Model

```bash
ollama stop qwen3:8b
```

---

# 9. Remove a Model

```bash
ollama rm qwen3:8b
```

---

# 10. Update a Model

Run the pull command again.

```bash
ollama pull qwen3:8b
```

If a newer version exists, Ollama downloads only the updated parts.

---

# 11. Start Ollama Server

```bash
ollama serve
```

Default API endpoint:

```
http://localhost:11434
```

---

# 12. Show Help

```bash
ollama --help
```

Shows every available command.

---

# 13. Use Ollama API

PowerShell example:

```powershell
Invoke-RestMethod `
  -Uri http://localhost:11434/api/generate `
  -Method Post `
  -ContentType "application/json" `
  -Body '{"model":"qwen3:8b","prompt":"Hello"}'
```

---

# 14. Most Used Commands

| Command | Description |
|----------|-------------|
| `ollama --version` | Show Ollama version |
| `ollama list` | List installed models |
| `ollama pull <model>` | Download a model |
| `ollama run <model>` | Start chatting with a model |
| `ollama show <model>` | Display model information |
| `ollama ps` | Show running models |
| `ollama stop <model>` | Stop a running model |
| `ollama rm <model>` | Remove a model |
| `ollama serve` | Start the Ollama API server |
| `ollama --help` | Show all commands |

---

# Recommended Models

For GitHub Copilot Professional Certification:

```bash
ollama pull qwen3:8b
ollama pull llama3.1:8b
ollama pull deepseek-coder-v2:16b-lite
```

Priority:

1. Qwen3:8b ⭐⭐⭐⭐⭐
2. DeepSeek-Coder V2 Lite ⭐⭐⭐⭐⭐
3. Llama 3.1:8b ⭐⭐⭐⭐☆

---

# Practice Prompts

## Explain Code

```
Explain the following C# code line by line.
```

## Refactor

```
Refactor this code using SOLID principles.
```

## Unit Test

```
Generate xUnit test cases.
```

## SQL

```
Optimize this SQL query.
```

## Documentation

```
Generate XML documentation comments.
```

## Git

```
Explain this Git command.
```

## Debug

```
Find the bug in this code.
```

---

# Learning Roadmap

- ✅ Install Ollama
- ✅ Download Models
- ✅ Basic Commands
- ⬜ VS Code Integration
- ⬜ Continue Extension
- ⬜ Cline Extension
- ⬜ GitHub Copilot Comparison
- ⬜ Prompt Engineering
- ⬜ Local AI Coding Workflow
- ⬜ Build AI Projects

---

Happy Learning! 🚀