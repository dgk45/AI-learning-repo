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

---

# 151. Start Ollama Server

Starts the Ollama API server.

```cmd
ollama serve
```

Default Endpoint

```
http://localhost:11434
```

---

# 152. Run a Model

Loads a model and starts an interactive chat.

```cmd
ollama run qwen3:8b
```

Example

```cmd
ollama run llama3.1:8b
```

---

# 153. Pull (Download) a Model

Downloads a model from the Ollama Registry.

```cmd
ollama pull qwen3:8b
```

---

# 154. Push a Model

Uploads your custom model to the Ollama Registry.

```cmd
ollama push mymodel
```

Requires login.

---

# 155. List Installed Models

```cmd
ollama list
```

---

# 156. Show Running Models

```cmd
ollama ps
```

---

# 157. Stop a Running Model

```cmd
ollama stop qwen3:8b
```

---

# 158. Remove a Model

```cmd
ollama rm qwen3:8b
```

---

# 159. Copy a Model

Creates another copy with a different name.

```cmd
ollama cp qwen3:8b my-qwen
```

---

# 160. Create a Custom Model

Creates a model from a Modelfile.

```cmd
ollama create mymodel -f Modelfile
```

---

# 161. Show Model Details

```cmd
ollama show qwen3:8b
```

---

# 162. Show Model License

```cmd
ollama show qwen3:8b --license
```

---

# 163. Show Model Parameters

```cmd
ollama show qwen3:8b --parameters
```

---

# 164. Show Modelfile

```cmd
ollama show qwen3:8b --modelfile
```

---

# 165. Display Help

```cmd
ollama help
```

or

```cmd
ollama --help
```

---

# 166. Show Version

```cmd
ollama --version
```

---

# 167. Verbose Output

```cmd
ollama --verbose
```

Useful for troubleshooting.

---

# 168. Display Help for a Specific Command

```cmd
ollama run --help
```

Example

```cmd
ollama pull --help
```

---

# 169. List Available Commands

```cmd
ollama
```

---

# 170. Pull Latest Version

```cmd
ollama pull llama3.1:latest
```

---

# 171. Pull Specific Version

```cmd
ollama pull qwen3:8b
```

---

# 172. Pull by Digest

```cmd
ollama pull model@sha256:<digest>
```

---

# 173. Run with Prompt

```cmd
ollama run qwen3:8b "Explain SOLID Principles."
```

---

# 174. Run with Multiline Prompt

```cmd
ollama run qwen3:8b
```

Then type your prompt.

---

# 175. Show Running Model Memory

```cmd
ollama ps
```

Displays model size and processor usage.

---

# 176. Remove Multiple Models

```cmd
ollama rm llama3.1:8b
ollama rm qwen3:8b
```

---

# 177. Copy and Modify a Model

```cmd
ollama cp qwen3:8b qwen3-dev
```

---

# 178. Create Model from Existing Model

Example Modelfile

```text
FROM qwen3:8b

SYSTEM You are an expert .NET developer.
```

Create

```cmd
ollama create dotnet-ai -f Modelfile
```

---

# 179. Show Custom Model

```cmd
ollama show dotnet-ai
```

---

# 180. Remove Custom Model

```cmd
ollama rm dotnet-ai
```

---

# 181. Start Chat

```cmd
ollama run qwen3:8b
```

---

# 182. Exit Chat

Press

```
Ctrl + C
```

---

# 183. View Local API

Open

```
http://localhost:11434
```

---

# 184. Generate Using REST API

```cmd
curl http://localhost:11434/api/generate ^
-d "{\"model\":\"qwen3:8b\",\"prompt\":\"Hello\"}"
```

---

# 185. Chat API

```cmd
curl http://localhost:11434/api/chat
```

---

# 186. List Local Models via API

```cmd
curl http://localhost:11434/api/tags
```

---

# 187. Show Model Information via API

```cmd
curl http://localhost:11434/api/show
```

---

# 188. Embeddings API

```cmd
curl http://localhost:11434/api/embed
```

---

# 189. Generate Embeddings

```cmd
curl http://localhost:11434/api/embeddings
```

---

# 190. Check Server Availability

```cmd
curl http://localhost:11434
```

---

# 191. Find Ollama Process

```cmd
tasklist | findstr ollama
```

---

# 192. Kill Ollama

```cmd
taskkill /F /IM ollama.exe
```

---

# 193. Restart Ollama

```cmd
taskkill /F /IM ollama.exe
ollama serve
```

---

# 194. Check Environment Variable

```cmd
echo %OLLAMA_MODELS%
```

---

# 195. Check Model Folder

```cmd
dir %OLLAMA_MODELS%
```

---

# 196. List Environment Variables

```cmd
set OLLAMA
```

---

# 197. Show Port Usage

```cmd
netstat -ano | findstr 11434
```

---

# 198. Check Ollama Logs

Logs are typically located under your user profile's Ollama directory or the configured data directory.

---

# 199. Verify Installed Model

```cmd
ollama show qwen3:8b
```

---

# 200. Complete Ollama Status Check

```cmd
ollama --version
ollama list
ollama ps
echo %OLLAMA_MODELS%
netstat -ano | findstr 11434
```

---

Happy Learning! 🚀
