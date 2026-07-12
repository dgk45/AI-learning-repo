# Ollama Basics

Beginner-friendly Ollama command guide for local model practice, coding practice, and API testing.

---

## Table of Contents

1. What Ollama Does
2. Check Installation
3. Start the Ollama Server
4. Download Models
5. List Installed Models
6. Run a Model
7. Interactive Chat Commands
8. Model Information
9. Manage Models
10. Update Models
11. Use the Local API
12. Thinking Mode
13. Troubleshooting
14. Recommended Models
15. Practice Prompts
16. Quick Command Reference

---

## 1. What Ollama Does

Ollama lets you download and run large language models locally on your computer.

Common uses:

- Chat with local AI models.
- Test coding prompts.
- Run local API calls.
- Create custom models with a `Modelfile`.
- Practice prompt engineering without depending on a cloud API.

---

## 2. Check Installation

Use this command to check whether Ollama is installed.

```cmd
ollama --version
```

Example output:

```text
ollama version 0.10.x
```

To show all available Ollama commands:

```cmd
ollama --help
```

You can also show help for a specific command:

```cmd
ollama run --help
ollama pull --help
```

---

## 3. Start the Ollama Server

The Ollama server is required for API access.

```cmd
ollama serve
```

Default local endpoint:

```text
http://localhost:11434
```

To quickly check whether the server is responding:

```cmd
curl http://localhost:11434
```

Note: In many installations, Ollama starts automatically in the background. Use `ollama serve` when you need to start it manually.

---

## 4. Download Models

Use `ollama pull` to download a model.

```cmd
ollama pull qwen3:8b
```

Other examples:

```cmd
ollama pull llama3.1:8b
ollama pull gemma3:12b
ollama pull deepseek-coder-v2:16b-lite
```

Download the latest version of a model:

```cmd
ollama pull llama3.1:latest
```

Pull a model by digest:

```cmd
ollama pull model@sha256:<digest>
```

Explanation:

- `pull` downloads the model.
- `<model>` is the model name.
- A tag such as `:8b` or `:latest` identifies the version or size.
- Pulling the same model again updates it if a newer version exists.

---

## 5. List Installed Models

Use this command to see models already downloaded on your machine.

```cmd
ollama list
```

Example output:

```text
NAME                SIZE
qwen3:8b            5.2 GB
llama3.1:8b         4.9 GB
gemma3:12b          8.1 GB
```

---

## 6. Run a Model

Start an interactive chat session:

```cmd
ollama run qwen3:8b
```

Run another installed model:

```cmd
ollama run llama3.1:8b
```

Run a model with a prompt directly from the command line:

```cmd
ollama run qwen3:8b "Explain SOLID principles."
```

Explanation:

- `run` loads the model.
- If no prompt is provided, Ollama opens an interactive chat.
- If a prompt is provided, Ollama answers directly in the terminal.

---

## 7. Interactive Chat Commands

These commands work inside an `ollama run <model>` chat session.

| Command | Purpose |
| --- | --- |
| `/show` | Show information about the current model |
| `/set` | View or change session settings |
| `/load <model>` | Load a model or saved session |
| `/save <name>` | Save the current session |
| `/clear` | Clear the current conversation context |
| `/bye` | Exit the chat session |
| `/help` | Show interactive help |
| `/?` | Show interactive help |
| `/? shortcuts` | Show keyboard shortcuts |

Example session:

```text
C:\> ollama run qwen3:8b

>>> /show
>>> /set
>>> /clear
>>> /save my-session
>>> /load my-session
>>> /help
>>> /bye
```

Exit options:

```text
/bye
```

You can also use:

```text
Ctrl + D
```

Use `Ctrl + C` when you need to interrupt the current response.

---

## 8. Model Information

Show full model details:

```cmd
ollama show qwen3:8b
```

Show the model license:

```cmd
ollama show qwen3:8b --license
```

Show model parameters:

```cmd
ollama show qwen3:8b --parameters
```

Show the model's Modelfile:

```cmd
ollama show qwen3:8b --modelfile
```

Typical information includes:

- Model family
- Parameter size
- Context size
- Quantization
- License
- Modelfile settings

---

## 9. Manage Models

Show running models:

```cmd
ollama ps
```

Stop a running model:

```cmd
ollama stop qwen3:8b
```

Remove a model:

```cmd
ollama rm qwen3:8b
```

Remove multiple models:

```cmd
ollama rm llama3.1:8b
ollama rm qwen3:8b
```

Copy a model with a new name:

```cmd
ollama cp qwen3:8b my-qwen
```

Push a custom model to the Ollama Registry:

```cmd
ollama push mymodel
```

Note: `ollama push` requires login and is mainly used for custom models.

---

## 10. Update Models

To update a model, pull it again.

```cmd
ollama pull qwen3:8b
```

If a newer version exists, Ollama downloads only the changed parts.

---

## 11. Use the Local API

Ollama provides a local HTTP API at:

```text
http://localhost:11434
```

### Generate Text

Command Prompt example:

```cmd
curl http://localhost:11434/api/generate ^
  -d "{\"model\":\"qwen3:8b\",\"prompt\":\"Hello\",\"stream\":false}"
```

PowerShell example:

```powershell
Invoke-RestMethod `
  -Uri http://localhost:11434/api/generate `
  -Method Post `
  -ContentType "application/json" `
  -Body '{"model":"qwen3:8b","prompt":"Hello","stream":false}'
```

### Chat API

```cmd
curl http://localhost:11434/api/chat
```

### List Local Models via API

```cmd
curl http://localhost:11434/api/tags
```

### Show Model Information via API

```cmd
curl http://localhost:11434/api/show
```

### Embeddings API

```cmd
curl http://localhost:11434/api/embed
```

Older examples may also use:

```cmd
curl http://localhost:11434/api/embeddings
```

---

## 12. Thinking Mode

Some models, such as Qwen3, support thinking mode.

Enable thinking inside an interactive session:

```text
/set think
```

Disable thinking inside an interactive session:

```text
/set nothink
```

Run from the command line and hide thinking output:

```cmd
ollama run qwen3:8b --hidethinking "Explain SOLID principles."
```

Disable thinking from the command line:

```cmd
ollama run qwen3:8b --think=false "Hello"
```

Enable thinking from the command line:

```cmd
ollama run qwen3:8b --think=true "Explain SOLID principles."
```

API example with thinking disabled:

```json
{
  "model": "qwen3:8b",
  "think": false,
  "prompt": "Hello"
}
```

API example with streaming disabled:

```json
{
  "model": "qwen3:8b",
  "stream": false,
  "prompt": "Hello"
}
```

Explanation:

- `/set think` enables thinking mode during chat.
- `/set nothink` disables thinking mode during chat.
- `--hidethinking` hides the thinking text while still allowing supported models to reason internally.
- `--think=false` disables thinking for supported models.
- `stream=false` returns one complete JSON response instead of multiple streamed JSON objects.

---

## 13. Troubleshooting

Find the Ollama process:

```cmd
tasklist | findstr ollama
```

Stop the Ollama process:

```cmd
taskkill /F /IM ollama.exe
```

Restart Ollama manually:

```cmd
taskkill /F /IM ollama.exe
ollama serve
```

Check the model folder environment variable:

```cmd
echo %OLLAMA_MODELS%
```

Open the configured model folder:

```cmd
dir %OLLAMA_MODELS%
```

List Ollama-related environment variables:

```cmd
set OLLAMA
```

Check port usage for the Ollama API port:

```cmd
netstat -ano | findstr 11434
```

Complete status check:

```cmd
ollama --version
ollama list
ollama ps
echo %OLLAMA_MODELS%
netstat -ano | findstr 11434
```

Logs are usually located under your user profile's Ollama directory or the configured data directory.

---

## 14. Recommended Models

Useful models for coding and GitHub Copilot certification practice:

```cmd
ollama pull qwen3:8b
ollama pull llama3.1:8b
ollama pull deepseek-coder-v2:16b-lite
```

Suggested priority:

1. `qwen3:8b` - strong general and reasoning practice model.
2. `deepseek-coder-v2:16b-lite` - useful for coding tasks.
3. `llama3.1:8b` - good general-purpose model.

---

## 15. Practice Prompts

### Explain Code

```text
Explain the following C# code line by line.
```

### Refactor Code

```text
Refactor this code using SOLID principles.
```

### Generate Unit Tests

```text
Generate xUnit test cases for this method.
```

### Optimize SQL

```text
Optimize this SQL query and explain the changes.
```

### Write Documentation

```text
Generate XML documentation comments for this C# class.
```

### Explain Git

```text
Explain this Git command and when I should use it.
```

### Debug Code

```text
Find the bug in this code and suggest a corrected version.
```

---

## 16. Quick Command Reference

| Command | Description |
| --- | --- |
| `ollama --version` | Show installed Ollama version |
| `ollama --help` | Show general help |
| `ollama serve` | Start the Ollama server |
| `ollama pull <model>` | Download or update a model |
| `ollama list` | List installed models |
| `ollama run <model>` | Start chatting with a model |
| `ollama run <model> "<prompt>"` | Run a model with one prompt |
| `ollama show <model>` | Show model details |
| `ollama ps` | Show running models |
| `ollama stop <model>` | Stop a running model |
| `ollama rm <model>` | Remove a model |
| `ollama cp <model> <new-name>` | Copy a model to a new local name |
| `ollama create <name> -f Modelfile` | Create a custom model |
| `ollama push <model>` | Upload a custom model |

---

## Simple Learning Order

Follow this order if you are new to Ollama:

1. Check installation with `ollama --version`.
2. Start the server with `ollama serve` if needed.
3. Download a model with `ollama pull qwen3:8b`.
4. Confirm it installed with `ollama list`.
5. Start chat with `ollama run qwen3:8b`.
6. Exit chat with `/bye`.
7. View model details with `ollama show qwen3:8b`.
8. Test the API at `http://localhost:11434`.
9. Use `ollama ps` and `ollama stop <model>` to manage running models.
10. Use `ollama rm <model>` when you no longer need a model.

---

Happy learning!
