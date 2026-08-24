# Claude Certified Architect — Easy Study Notes

> These notes explain every topic in **simple English**, step by step, like a teacher explaining it for the very first time. Each topic includes: a simple meaning, a real-world example, a sample output, and a quick revision box.

---

# CHAPTER 0: About the Exam (Read This First)

## 0.1 What is this Certification?

**Claude Certified Architect — Foundations** is an exam that checks if you can make good decisions while building real Claude-based systems.

**In Simple Words:** It's not just about knowing facts — it tests whether you can **choose the right approach** in real situations, like a driving test checks your judgment, not just traffic-sign trivia.

**It tests your knowledge of 4 core technologies:**
- **Claude API** – sending requests, tools, structured output
- **Claude Agent SDK** – building agents and multi-agent systems
- **Claude Code** – the coding assistant and its configuration
- **MCP (Model Context Protocol)** – connecting Claude to outside systems

---

## 0.2 Who Should Take This Exam?

**Target Candidate:** A **solution architect** who designs and ships production apps using Claude, with at least 6 months of hands-on experience in areas like agent orchestration, CLAUDE.md/MCP setup, prompt engineering, and CI/CD.

---

## 0.3 Exam Format

| Parameter | Value |
|---|---|
| Question type | Multiple choice (1 correct answer out of 4) |
| Scoring | 100–1000 scale; **passing score = 720** |
| Guessing penalty | **None** — always answer every question! |
| Scenarios | 4 out of 8 possible scenarios (chosen randomly) |

> 💡 **Tip:** Since there's no penalty for wrong guesses, **never leave a question blank**.

---

## 0.4 The 5 Exam Domains (Know These Weights!)

**In Simple Words:** These are the 5 "chapters" of knowledge the exam is built from — the percentage shows how many questions come from each area, so **study the heaviest ones first**.

| Domain | Weight | Roughly Matches |
|---|---|---|
| 1. Agent architecture and orchestration | **27%** | Chapter 3 |
| 2. Tool design and MCP integration | **18%** | Chapters 2 & 4 |
| 3. Claude Code configuration and workflows | **20%** | Chapter 5 |
| 4. Prompt engineering and structured output | **20%** | Chapters 2, 6 & 7 |
| 5. Context management and reliability | **15%** | Chapters 9, 10, 11 & 12 |

> 🎯 **Study Priority:** Domain 1 (27%) and Domains 3 & 4 (20% each) together make up **67%** of the exam — focus your revision time there first.

---

## 0.5 The 8 Exam Scenarios

**In Simple Words:** Real exam questions are wrapped inside these 8 story situations. Knowing the scenarios helps you recognize what a question is really testing.

| # | Scenario | What It's About |
|---|---|---|
| 1 | **Customer Support Agent** | Handling returns/billing using MCP tools; escalation |
| 2 | **Code Generation with Claude Code** | Slash commands, CLAUDE.md, planning mode |
| 3 | **Multi-Agent Research System** | Coordinator + subagents; reports with citations |
| 4 | **Developer Productivity Tools** | Exploring codebases with built-in tools + MCP |
| 5 | **Claude Code for CI/CD** | Automated code review, test generation, PR feedback |
| 6 | **Structured Data Extraction** | JSON schemas, validation, edge cases |
| 7 | **Conversational AI Architecture** | Context windows, memory, ambiguous input handling |
| 8 | **Agentic AI Tools** | *(Rare/less documented scenario)* |

---

# CHAPTER 1: Talking to Claude (The API Basics)

## 1.1 API Request

**API Request** is the message your program sends to Claude, and the reply Claude sends back.

**In Simple Words:** It's like sending a text message and getting a reply — except the "text message" has to follow a specific format that Claude understands.

**A request has 4 main parts:**
- **model** – which Claude model should answer
- **max_tokens** – the maximum length allowed for the reply
- **system** – the instructions/rules Claude must follow
- **messages** – the conversation so far

**Real-world example:** Ordering food at a restaurant.
- You (the app) → give your order to the waiter (the API request)
- The kitchen (Claude) → cooks and sends back the food (the response)

**Sample Output — what you send:**
```json
{
  "model": "claude-sonnet-4-6",
  "max_tokens": 1024,
  "system": "You are a friendly assistant.",
  "messages": [
    {"role": "user", "content": "Hi!"}
  ]
}
```

**Sample Output — what you get back:**
```json
{
  "role": "assistant",
  "content": [{"type": "text", "text": "Hello! How can I help you today?"}],
  "stop_reason": "end_turn"
}
```

> ⚠️ **Important Rule:** Claude does **not** remember past conversations on its own. Your program must **resend the entire conversation every time** — like re-reading a whole letter before writing your next reply.

---

## 1.2 Message Roles

**Message Role** is a label that shows **who** sent each message in the conversation.

**In Simple Words:** Think of a group chat — every message shows the sender's name, so you know who said what.

| Role | Who it is | Simple Meaning |
|---|---|---|
| `user` | You / the customer | A question, or a tool's answer sent back to Claude |
| `assistant` | Claude | Claude's reply, or Claude asking to use a tool |
| `system` | The instructions | The "rules of the game," set once at the start |

**Sample Output:**
```json
{"role": "user", "content": "What's the weather?"}
{"role": "assistant", "content": "Let me check that for you."}
```

> **Note:** Even a tool's result is sent back as a `user` message (not a separate "tool" role) — it is simply wrapped inside a `tool_result`.

---

## 1.3 Stop Reason

**Stop Reason** tells you **why** Claude stopped replying.

**In Simple Words:** It's Claude's way of saying "I'm done" or "I need something from you first."

| Value | Simple Meaning |
|---|---|
| `end_turn` | ✅ Claude has fully finished — show the answer |
| `tool_use` | 🛠️ Claude wants to use a tool — go run it |
| `max_tokens` | ✂️ Claude got cut off — the answer may be incomplete |

**Sample Output:**
```json
{"stop_reason": "tool_use"}
```
This tells your program: *"Stop here, go run a tool, then come back and continue."*

---

## 1.4 System Prompt

**System Prompt** is the set of instructions you give Claude **before** the conversation starts.

**In Simple Words:** It's like a job description you hand to a new employee on their first day — it tells them how to behave.

**Sample Output:**
```
system: "You are a customer support agent. Always be polite.
Verify the customer's identity before discussing their order."
```

> ⚠️ **Be careful with wording.** If you write "always verify the customer," Claude may call the verification tool even when it isn't needed — just like an overly cautious new employee who double-checks everything.

---

## 1.5 Context Window

**Context Window** is the total amount of text Claude can "see" or hold in mind at one time.

**In Simple Words:** Imagine a desk that can only hold so many papers. If you pile on too much, the old papers fall off the edge and get forgotten.

**3 common problems:**

1. **Lost in the Middle**
   - Claude notices information at the **start** and **end** of a long text better than the **middle**.
   - **Fix:** Put important facts first or last.

2. **Tool Result Clutter**
   - If a tool returns 40 pieces of data but you only need 5, the extra 35 waste space.

3. **Fuzzy Summaries**
   - When old chat gets compressed/summarized, exact numbers (like "$150.00") can turn vague ("around $150").

**Sample Output:**
```
GOOD:
[KEY FACTS AT TOP] Customer wants a refund of $89.99
... (supporting detail in the middle) ...
[ACTION NEEDED AT BOTTOM] Approve refund

BAD:
... (the $89.99 refund fact is buried on page 5 of 10) ...
```

---

### ✅ Key Points to Remember — Chapter 1
- Claude has **no memory** — resend the full conversation every time.
- Messages are labeled with **roles**: `user`, `assistant`, `system`.
- `stop_reason` tells you whether Claude is done (`end_turn`) or needs a tool run (`tool_use`).
- The **system prompt** sets Claude's behavior rules before the chat starts.
- The **context window** is limited — place key facts at the start or end, not buried in the middle.

### 🔁 Quick Revision — Chapter 1
| Concept | One-line Meaning |
|---|---|
| API Request | Message sent to Claude |
| Message Role | Who sent the message |
| Stop Reason | Why Claude stopped talking |
| System Prompt | Rules set before the chat |
| Context Window | Claude's limited "memory desk" |

### 💡 One Simple Example
You ask Claude "What's 5 + 5?" → Claude replies "10" with `stop_reason: "end_turn"`, meaning it fully finished answering and no further action is needed.

---

# CHAPTER 2: Tools — Giving Claude "Hands"

## 2.1 Tool Use

**Tool Use** is how Claude asks your program to **perform an action** (like checking a database) — because Claude cannot run code or access outside systems by itself.

**In Simple Words:** Claude is like a manager who cannot personally go to the warehouse. It asks an employee (your code) to go check and report back.

**Step-by-step — how the loop works:**
1. Claude says: *"I need to call `get_customer`."*
2. Your code runs `get_customer()`.
3. Your code sends the result back to Claude.
4. Claude uses that result to continue answering.

---

## 2.2 Defining a Tool

**Tool Definition** is the description that tells Claude what a tool does and how to use it.

**In Simple Words:** Imagine a food delivery app menu. If an item is just called "Chicken Dish," you don't know what you're ordering. But if it says "Grilled chicken breast with lemon and herbs, served with rice," you know exactly what you'll get. In the same way, Claude only understands a tool through its **description** — a vague description confuses Claude, a detailed one makes usage clear.

**Sample Output — a well-written tool:**
```json
{
  "name": "get_customer",
  "description": "Finds a customer by email or ID. Returns name, email, order history. Use this BEFORE lookup_order to verify identity.",
  "input_schema": {
    "type": "object",
    "properties": {
      "email": {"type": "string", "description": "Customer email"}
    }
  }
}
```

> 📝 **Golden Rule:** The **description is everything**. If two tools sound similar (`analyze_content` vs `analyze_document`), Claude gets confused — just like you would, reading two nearly identical menu items.

---

## 2.3 Tool Choice

**Tool Choice** is a setting that controls **how much freedom** Claude has when deciding to use tools.

**In Simple Words:** It's like telling an employee: "use your judgment" vs. "you MUST fill out this form" vs. "you MUST use THIS exact form."

| Setting | Simple Meaning |
|---|---|
| `auto` | Claude decides on its own — the normal default |
| `any` | Claude **must** use *some* tool |
| `{"type": "tool", "name": "X"}` | Claude **must** use tool `X` specifically |

**Sample Output:**
```json
"tool_choice": {"type": "tool", "name": "extract_metadata"}
```
This forces Claude to always start with `extract_metadata` first.

---

## 2.4 JSON Schemas

**JSON Schema** is a "form template" that forces Claude's answer into a clean, predictable structure instead of free-flowing text.

**In Simple Words:** It's like giving someone a fill-in-the-blanks form instead of a blank sheet of paper — you always get the same fields back, in the same shape.

**Sample Output:**
```json
{
  "category": "bug",
  "severity": "high",
  "confidence": 0.87,
  "category_detail": null
}
```

**3 rules for building good schemas:**
- Only mark a field as **required** if it is *always* available — otherwise Claude may invent a value to fill it.
- Allow **null** for optional information — so Claude can honestly say "I don't know."
- Add an **"other"** option to category lists (enums), so nothing gets lost if it doesn't fit a category.

> ⚠️ A schema only guarantees the **shape** is correct (no broken JSON). It does **not** guarantee the **values** are correct — Claude could still put a wrong number in the right box.

---

## 2.5 Two Kinds of Mistakes

| Error Type | Example | Simple Fix |
|---|---|---|
| **Syntax Error** | Broken JSON, missing bracket | Use a schema (`tool_use`) — this nearly eliminates it |
| **Semantic Error** | Numbers don't add up, wrong value used | Double-check the answer / ask Claude to re-verify |

---

### ✅ Key Points to Remember — Chapter 2
- **Tools** let Claude perform real actions through your code.
- A **clear tool description** is the most important part of a tool definition.
- **tool_choice** controls whether Claude must use a tool, and which one.
- **JSON Schemas** guarantee correct *shape*, not correct *values*.
- Errors are either **syntax errors** (structure) or **semantic errors** (meaning).

### 🔁 Quick Revision — Chapter 2
| Concept | One-line Meaning |
|---|---|
| Tool Use | Claude asks your code to act |
| Tool Definition | Description of what a tool does |
| Tool Choice | Controls how tools are picked |
| JSON Schema | Template that forces structured answers |

### 💡 One Simple Example
A weather tool named `get_weather` with a clear description ("Returns current temperature for a city") lets Claude correctly call it when a user asks "What's the weather in Chennai?"

---

# CHAPTER 3: Building "Agents" (Claude That Acts on Its Own)

## 3.1 The Agentic Loop

**Agentic Loop** is a repeating cycle where Claude keeps working step by step until a task is fully complete.

**In Simple Words:** Like a chef who keeps tasting and adjusting a dish, again and again, until it is finally ready to serve.

**Step-by-step:**
1. Ask Claude to do the task.
2. **If** Claude says *"I need a tool"* → run the tool → send the result back → ask Claude again.
3. **If** Claude says *"I'm done"* (`stop_reason: end_turn`) → show the final answer.

**Sample Output — a mini loop trace:**
```
Turn 1: Claude → tool_use: lookup_order(id=123)
Turn 2: Your code → runs lookup_order, gets {"status": "shipped"}
Turn 3: Claude → "Your order has shipped!" (stop_reason: end_turn)
```

> ⚠️ **Common Mistake:** Don't guess that Claude is "done" by scanning for words like "task completed," or by stopping after a fixed number of tries. The **only** reliable signal is `stop_reason == "end_turn"`.

---

## 3.2 Agent Definition

**Agent Definition** is a blueprint describing one agent's name, job, and which tools it's allowed to use.

**In Simple Words:** It's like an employee's ID badge — it shows their name, their department, and which doors they're allowed to open.

**Sample Output:**
```python
agent = AgentDefinition(
    name="customer_support",
    system_prompt="You are a customer support agent...",
    allowed_tools=["get_customer", "lookup_order", "process_refund"]
)
```

> **Rule — Least Privilege:** Only give an agent the tools it truly needs. Like giving an intern keys only to their own office, not the whole building.

---

## 3.3 Coordinator and Subagents (Hub and Spoke)

**Coordinator** is a "manager" agent that hands off smaller jobs to specialist "worker" agents (**subagents**).

**In Simple Words:** Like a restaurant manager assigning tasks to the grill cook, the salad chef, and the dessert chef — each does their own part, then reports back.

```
         Coordinator (Manager)
        /         |          \
   Web Search   Document      Report
   Subagent     Analyzer      Writer
```

> ⚠️ **Very Important Rule:** Subagents have **no memory** of the manager's conversation. If you don't give them the details directly, they know nothing — like hiring a temp worker who has never seen your files unless you hand them a folder.

**Sample Output — bad vs. good delegation:**
```
❌ BAD:  Task: "Analyze the document"
✅ GOOD: Task: "Analyze this document: [full text].
          Focus on financial risks. Return a JSON summary."
```

---

## 3.4 The Task Tool

**Task Tool** is the specific tool a manager agent uses to "hire" a subagent for a job.

**In Simple Words:** It's like picking up the phone and calling a specialist consultant for one specific question.

**Sample Output — running 3 helpers at once:**
```
Task 1: "Search for articles about AI music"
Task 2: "Analyze document Y for key facts"
Task 3: "Search for articles about AI film"
→ All three run at the same time (in parallel), saving time.
```

---

## 3.5 Hooks

**Hooks** are pieces of code that run **automatically** at key moments — either to clean up data, or to block risky actions.

**In Simple Words:** A security guard at a door **always** checks ID, no matter how nicely someone asks to skip it. Compare this to simply *asking* people nicely to show ID — some might sneak past.

| | Hooks | Instructions in the Prompt |
|---|---|---|
| Reliability | 100% guaranteed | Usually works, but not guaranteed |
| Best used for | Money limits, legal rules, safety | General style preferences |

**Sample Output — a safety hook:**
```python
@hook("PreToolUse")
def enforce_refund_limit(tool_call):
    if tool_call.name == "process_refund" and tool_call.args.amount > 500:
        return redirect_to_escalation(tool_call)
```

> **Rule:** If breaking a rule could cost real money or cause real harm, use a **hook** — not just a polite instruction.

---

### ✅ Key Points to Remember — Chapter 3
- The **agentic loop** repeats until `stop_reason == "end_turn"`.
- **AgentDefinition** = name + job + allowed tools (follow least privilege).
- **Subagents have no memory** — always give them full context.
- Use the **Task tool** to delegate work to subagents, often in parallel.
- Use **hooks** (not just instructions) to enforce hard rules like money limits.

### 🔁 Quick Revision — Chapter 3
| Concept | One-line Meaning |
|---|---|
| Agentic Loop | Repeat until Claude signals it's done |
| Agent Definition | Blueprint for one agent |
| Coordinator/Subagents | Manager delegates to specialist workers |
| Task Tool | Tool used to call a subagent |
| Hooks | Guaranteed automatic safety checks |

### 💡 One Simple Example
A coordinator agent gets a task "research a topic and write a report." It sends one subagent to search the web and another to analyze a document — both run at the same time — then combines their results into the final report.

---

# CHAPTER 4: MCP (Model Context Protocol) — The Universal Plug

## 4.1 What is MCP?

**MCP (Model Context Protocol)** is a standard way to connect Claude to outside systems, like Jira, GitHub, or your own database.

**In Simple Words:** It's like a USB port — one standard plug shape that lets you connect many different devices.

**MCP gives Claude 3 things:**
- **Tools** – actions it can perform (e.g., "create ticket")
- **Resources** – data it can read (e.g., a list of all your files)
- **Prompts** – ready-made question templates

---

## 4.2 MCP Servers

**MCP Server** is a small program that plugs into Claude and offers a set of tools.

**In Simple Words:** It's like installing an app on your phone — once installed, its features just show up and are ready to use.

**Sample Output — `.mcp.json` (shared with the whole team):**
```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {"GITHUB_TOKEN": "${GITHUB_TOKEN}"}
    }
  }
}
```

| File | Purpose |
|---|---|
| `.mcp.json` (project-level) | Shared with your whole team |
| `~/.claude.json` (personal) | Just for you, for testing new things |

---

## 4.3 isError Flag

**isError** is a flag that tells Claude a tool call **failed**, ideally along with the reason why.

**In Simple Words:** Instead of a store just saying "Sorry, error," a good store says "Sorry, we're out of stock, but here's a similar item, and it'll be back Tuesday."

**Sample Output — a GOOD, helpful error:**
```json
{
  "isError": true,
  "content": {
    "errorCategory": "transient",
    "isRetryable": true,
    "message": "Timeout while calling the orders API."
  }
}
```

**Sample Output — a BAD, unhelpful error:**
```json
{"isError": true, "content": "Operation failed"}
```
The bad version leaves Claude guessing whether to retry, give up, or tell the user. A good error message answers those questions in advance.

---

## 4.4 MCP Resources

**MCP Resources** are read-only reference materials Claude can look at, without needing to "do" anything.

**In Simple Words:** It's like a library's card catalog — you can browse what's available before deciding what to actually check out.

---

### ✅ Key Points to Remember — Chapter 4
- **MCP** is a universal connector between Claude and outside tools/data.
- **MCP Servers** provide the actual tools, like installed apps.
- Use **isError** with a clear reason so Claude knows how to respond.
- **Resources** are read-only reference data, not actions.

### 🔁 Quick Revision — Chapter 4
| Concept | One-line Meaning |
|---|---|
| MCP | Universal plug for connecting outside systems |
| MCP Server | Program offering a set of tools |
| isError | Flag showing a tool call failed, plus why |
| MCP Resources | Read-only reference data |

### 💡 One Simple Example
Connecting Claude to a GitHub MCP server lets Claude create issues, read pull requests, and comment — without you writing custom integration code.

---

# CHAPTER 5: Claude Code — Your Coding Assistant Setup

## 5.1 CLAUDE.md

**CLAUDE.md** is a file where you write standing instructions for Claude Code to always follow.

**In Simple Words:** It's like a sticky note on an employee's desk with "house rules" they should always remember.

**3 levels of CLAUDE.md files:**
```
1. ~/.claude/CLAUDE.md      → just for YOU (personal habits)
2. .claude/CLAUDE.md        → for the WHOLE TEAM (project rules)
3. subfolder/CLAUDE.md      → only for that specific folder
```

> ⚠️ **Common Mistake:** Putting team rules in your *personal* file (level 1) by accident — new teammates will never see them.

---

## 5.2 The @path Shortcut

**@path** is a shortcut to pull in content from another file, instead of copy-pasting it.

**In Simple Words:** Instead of retyping the same recipe in every cookbook, you just write "see page 42."

**Sample Output:**
```markdown
Coding standards are described in @./standards/coding-style.md
```

---

## 5.3 Conditional Rules Folder

**.claude/rules/** holds separate rule files that only "turn on" when you're editing matching files.

**In Simple Words:** It's like a dress code that only applies in the kitchen (chef whites), not in the office.

**Sample Output:**
```yaml
---
paths: ["**/*.test.tsx", "**/*.test.ts"]
---
Tests must use describe/it blocks.
Do not mock the database—use a test database.
```

**Why it's smart:** It saves space — Claude doesn't load "testing rules" while editing an unrelated file.

---

## 5.4 Slash Commands and Skills

**Slash Command** is a reusable shortcut you type as `/name` that runs a pre-written instruction.

**In Simple Words:** It's like a speed-dial button on an old phone — instead of typing the whole task every time, you press one shortcut.

**Sample Output:**
```yaml
---
context: fork
allowed-tools: ["Read", "Grep", "Glob"]
---
Analyze the code structure in the specified directory.
```
Typing `/review` runs this instantly.

**Quick meanings:**
- `context: fork` = do this quietly in the background (doesn't clutter your main chat)
- `allowed-tools` = a safety leash — the skill can't do MORE than this list allows

---

## 5.5 Skills (`.claude/skills/`)

**Skill** is an advanced, reusable command configured through a `SKILL.md` file with special settings ("frontmatter").

**In Simple Words:** A skill is like a slash command with extra safety settings attached — you decide exactly what it's allowed to touch.

**Sample Output — a SKILL.md file:**
```yaml
---
context: fork
allowed-tools: ["Read", "Grep", "Glob"]
argument-hint: "Path to the directory to analyze"
---

Analyze the code structure in the specified directory.
Output a report on dependencies and architectural patterns.
```

**Frontmatter settings explained:**

| Setting | Meaning |
|---|---|
| `context: fork` | Runs the skill in an isolated subagent — its verbose output does not clutter your main chat |
| `allowed-tools` | Restricts which tools the skill can use (e.g., stops it from deleting files if not permitted) |
| `argument-hint` | Shows a hint asking for input, if the skill is run without one |

**Skill vs. CLAUDE.md — when to use which:**

| Use a Skill when... | Use CLAUDE.md when... |
|---|---|
| You need it **on demand** for one specific task (review, analysis, generation) | You want rules **always loaded**, for general standards and conventions |

> **Tip:** For **personal skills** (`~/.claude/skills/`), create your own variants under different names so you don't accidentally affect your teammates' setup.

---

## 5.6 Planning Mode vs. Direct Execution

| Use Planning Mode when... | Use Direct Execution when... |
|---|---|
| Big changes, many files | One small clear fix |
| Multiple possible approaches | The fix is obvious |
| Unfamiliar codebase | You already understand the code |

**In Simple Words:** For a big house renovation, get a blueprint approved first (planning mode). For a leaky faucet, just fix it (direct execution).

---

## 5.7 Compact and Memory Commands

**/compact** squeezes the conversation to save space, like zipping a folder — but exact numbers can get fuzzy afterward.

**/memory** opens the CLAUDE.md file to save permanent notes for next time.

**In Simple Words:** `/compact` is cleaning your desk by summarizing old notes. `/memory` is writing something in your permanent notebook that you'll always carry with you.

---

## 5.8 Using Claude Code in CI/CD

**CI/CD Usage** means running Claude Code automatically, with no human watching, as part of automated software checks.

**In Simple Words:** It's like a vending machine instead of a cashier — no back-and-forth conversation, just: insert request, get result.

**Sample Output:**
```bash
claude -p "Review this PR for security issues" --output-format json
```
`-p` (print mode) runs once, prints the answer, and stops — perfect for automation, since there's no human there to answer follow-up questions.

---

## 5.9 Resuming and Forking Sessions

| Feature | Meaning |
|---|---|
| `--resume` | Continue a past conversation, like resuming a paused movie |
| `fork_session` | Split one conversation into two independent paths |

**Sample Output:**
```
Investigation
     |
 fork_session
  /         \
Plan A:     Plan B:
Redux       Context API
```

---

### ✅ Key Points to Remember — Chapter 5
- **CLAUDE.md** stores standing instructions, at personal, team, or folder level.
- **@path** links to another file instead of duplicating content.
- **.claude/rules/** applies rules only when matching files are touched.
- **Skills** (`SKILL.md`) = on-demand commands with tool restrictions (`allowed-tools`) and isolation (`context: fork`).
- Use **planning mode** for big/unfamiliar changes, **direct execution** for small clear fixes.
- **/compact** saves space (can lose precision); **/memory** saves permanent notes.
- Use **`-p`** (print mode) for CI/CD automation.

### 🔁 Quick Revision — Chapter 5
| Concept | One-line Meaning |
|---|---|
| CLAUDE.md | Standing instructions file |
| @path | Link to another file's content |
| .claude/rules/ | Rules that apply conditionally |
| Slash Command | Reusable `/shortcut` |
| Skill (SKILL.md) | On-demand command with tool restrictions |
| --resume / fork_session | Continue vs. split a session |

### 💡 One Simple Example
A `.claude/rules/` file set to trigger only on `*.test.ts` files ensures testing rules are loaded only while editing test files, keeping other tasks distraction-free.

---

# CHAPTER 6: Prompt Engineering — Talking to Claude Effectively

## 6.1 Few-Shot Prompting

**Few-Shot Prompting** means giving Claude a couple of examples of input → correct output, instead of only describing the rule in words.

**In Simple Words:** Teaching a kid to tie shoes by *showing* them 2–3 times works much better than only *describing* it in words.

**Sample Output:**
```
Text: "about two handfuls of rice"
-> {"amount": "~100g", "precision": "approximate"}

Text: "a pinch of salt"
-> {"amount": "~1g", "precision": "approximate"}
```
Now Claude can apply the same pattern to new cases (e.g., "a dash of pepper").

---

## 6.2 Clear Rules vs. Vague Instructions

**In Simple Words:** Telling a babysitter "put the kids to bed by 8:15pm, after teeth-brushing and one story" works far better than the vague "put them to bed at a reasonable time."

**Sample Output:**
```
❌ Vague: "Be conservative — report only high-confidence findings."

✅ Clear: Flag a comment ONLY if:
1. It contradicts the actual code
2. It references a function that doesn't exist
3. It's a leftover TODO for a bug already fixed
```

---

## 6.3 Prompt Chaining

**Prompt Chaining** means breaking one huge task into several smaller, focused steps done one at a time.

**In Simple Words:** Grading 10 exam papers **one at a time, carefully**, catches more mistakes than skimming all 10 at once.

**Sample Output:**
```
Step 1: Review auth.ts        → issues found in auth.ts
Step 2: Review database.ts    → issues found in database.ts
Step 3: Check how files connect → cross-file issues
```

---

## 6.4 The Interview Pattern

**Interview Pattern** means Claude asks clarifying questions **before** diving into a solution.

**In Simple Words:** A tailor measures you and asks about your style before cutting fabric, instead of guessing your size.

**Sample Output:**
```
Claude: "Before I build the caching system, quick questions:
1. Should the cache refresh automatically, or on a schedule?
2. Is it OK to show slightly old data if the cache is down?"
```

---

## 6.5 Validation and Retry

**Validation and Retry** means automatically checking Claude's output for mistakes, and asking it to fix them if something's wrong.

**In Simple Words:** A teacher hands back a math test saying "Question 3 is wrong, try again" instead of just marking it wrong and moving on.

**Sample Output:**
```
Error found: Field 'total' = 150, but sum(line_items) = 145.
→ Claude is asked to re-check and fix it.
```

> **Note:** This works well for math or format mistakes. It does **not** work if the information simply isn't in the document — Claude cannot invent facts that were never provided.

---

## 6.6 Self-Correction

**Self-Correction** means asking Claude to calculate something itself and compare it to a stated value, flagging any mismatch.

**Sample Output:**
```json
{
  "stated_total": "$150.00",
  "calculated_total": "$145.00",
  "conflict_detected": true
}
```

---

### ✅ Key Points to Remember — Chapter 6
- **Few-shot prompting** = show examples, don't just describe rules.
- **Clear, specific rules** work far better than vague instructions.
- **Prompt chaining** = break big tasks into small, focused steps.
- The **interview pattern** = ask clarifying questions before building.
- **Validation and retry** fixes math/format errors, not missing information.

### 🔁 Quick Revision — Chapter 6
| Concept | One-line Meaning |
|---|---|
| Few-Shot Prompting | Teach by example |
| Clear Rules | Specific > vague |
| Prompt Chaining | Big task → small steps |
| Interview Pattern | Ask before building |
| Validation & Retry | Check and fix output errors |

### 💡 One Simple Example
Instead of saying "summarize this well," a clear prompt says: "Summarize in exactly 3 bullet points, each under 15 words, covering: problem, cause, solution."

---

# CHAPTER 7: Message Batches API — Bulk Processing (Save Money!)

## 7.1 What is the Batches API?

**Message Batches API** lets you submit a large pile of requests at once and get all the answers later (up to 24 hours), at half the price of normal requests.

**In Simple Words:** Choosing "standard shipping" (cheaper, slower) instead of "next-day delivery" (pricier, instant) when you're not in a rush.

| | Batch API | Normal (Synchronous) API |
|---|---|---|
| Cost | 50% cheaper | Full price |
| Speed | Up to 24 hours | Instant |
| Best for | Big bulk jobs, not urgent | Live chats, urgent checks |

---

## 7.2 When to Use Which

**Sample Output — decision guide:**
```
✅ Batch:  Processing 10,000 old documents overnight
✅ Sync:   A customer waiting live for a chat reply
```

---

## 7.3 Custom ID

**custom_id** is a label you attach to each request in a batch, so you can match it back to its answer later.

**In Simple Words:** Writing your name on your exam paper before handing it into a huge stack — so it can be found again later.

**Sample Output:**
```json
{"custom_id": "doc-invoice-2024-001", "params": {...}}
```

---

## 7.4 Handling Failures

If a request in a batch fails, you can re-send **just that failed request** instead of the whole batch again.

---

## 7.5 Planning Around the 24-Hour Wait

**Sample Output:**
```
Need results by: 30 hours from now
Batch takes up to: 24 hours
→ You must submit the batch within the first 6 hours!
```

---

### ✅ Key Points to Remember — Chapter 7
- The **Batches API** is 50% cheaper but can take up to 24 hours.
- Use it for **large, non-urgent** jobs; use the normal API for live/urgent replies.
- **custom_id** lets you match each answer back to its original request.
- Only failed requests need to be resent, not the whole batch.

### 🔁 Quick Revision — Chapter 7
| Concept | One-line Meaning |
|---|---|
| Batches API | Cheaper, slower, bulk processing |
| custom_id | Label to match request ↔ answer |
| 24-hour rule | Plan submission time accordingly |

### 💡 One Simple Example
A company processes 5,000 old support tickets overnight using the Batches API, saving 50% compared to processing them one-by-one during the day.

---

# CHAPTER 8: Breaking Down Big Tasks

## 8.1 Fixed Pipelines

**Fixed Pipeline** is a task with steps that never change, done the same way every time.

**In Simple Words:** An assembly line — always Step A, then B, then C, in the same order.

```
Document → Extract metadata → Extract data → Validate → Final output
```
Good for predictable, repeatable jobs (e.g., always reviewing invoices the same way).

---

## 8.2 Dynamic Decomposition

**Dynamic Decomposition** means the next step is decided **after** seeing what was found, not planned in advance.

**In Simple Words:** A detective doesn't know their next move until they see the clues from the last one.

**Sample Output:**
```
1. "Add tests for old code"
2. → Found: 3 modules with no tests at all
3. → Decide: start with the riskiest one (payments)
4. → Found mid-way: it depends on an outside API
5. → Adapt: add a fake (mock) version of that API first
```

---

## 8.3 Multi-Pass Code Review

**Multi-Pass Code Review** means reviewing files one-by-one carefully first, **then** checking how they connect — instead of eyeballing everything at once.

**In Simple Words:** Proofreading each chapter of a book separately before checking if the whole story makes sense together.

---

### ✅ Key Points to Remember — Chapter 8
- **Fixed pipelines** = same steps every time, good for predictable jobs.
- **Dynamic decomposition** = next step depends on what was just discovered.
- **Multi-pass review** = check individual pieces first, then check connections.

### 🔁 Quick Revision — Chapter 8
| Concept | One-line Meaning |
|---|---|
| Fixed Pipeline | Same steps, every time |
| Dynamic Decomposition | Adapt steps based on findings |
| Multi-Pass Review | Individual files, then connections |

### 💡 One Simple Example
A code review first checks each file separately for bugs, then does a second pass checking if the files correctly call each other.

---

# CHAPTER 9: When to Ask a Human for Help (Escalation)

## 9.1 Clear Reasons to Escalate

**Escalation** means handing a task off to a real human, based on **clear rules** — not a "feeling."

**In Simple Words:** A cashier calls the manager over not because they "feel like it," but because of a clear store rule (e.g., "refunds over $500 need manager approval").

| Situation | What to Do |
|---|---|
| Customer says "get me a manager" | Escalate immediately |
| The policy doesn't cover this case | Escalate |
| Large amount of money is involved | Escalate (ideally auto-blocked by a hook) |

> ⚠️ **Unreliable Escalation Triggers (avoid these):**
> - "The customer sounds upset" (mood ≠ actual complexity)
> - "Claude rated its own confidence as low" (Claude can be confidently **wrong**)

---

## 9.2 Escalation Style

**In Simple Words:** Acknowledge feelings → offer a real solution → only escalate if they insist. Don't panic-escalate on the first sign of unhappiness.

**Sample Output:**
```
Customer: "This is outrageous, I'm unhappy with the quality!"
Agent: "I understand your frustration. I can offer a replacement or refund."
Customer: "No, I want to talk to someone!"
Agent: [NOW escalates — because they insisted again]
```

---

## 9.3 The Handoff Note

**Handoff Note** is a clean summary Claude writes for the human, since the human hasn't read the whole conversation.

**In Simple Words:** A nurse hands a doctor a clipboard summary of the patient, instead of the doctor reading the whole hospital chat log.

**Sample Output:**
```json
{
  "customer_name": "Ivan Petrov",
  "issue_summary": "Refund request for a damaged item",
  "actions_taken": ["Verified customer", "Offered replacement — declined"],
  "recommended_action": "Approve a full refund"
}
```

---

## 9.4 Confidence Scores and Spot-Checks

**In Simple Words:** A factory still randomly tests "perfect-looking" products, because looks can be deceiving.

**Sample Output:**
```
Overall accuracy: 97% (looks great!)
But for "handwritten invoices" specifically: only 60% accurate
→ Need to check accuracy BY CATEGORY, not just overall.
```

---

### ✅ Key Points to Remember — Chapter 9
- Escalate based on **clear rules**, never on "vibes" or self-reported confidence.
- Acknowledge and offer solutions **before** escalating.
- Write a clean **handoff note** so the human doesn't need to re-read everything.
- Check accuracy **by category**, not just the overall average.

### 🔁 Quick Revision — Chapter 9
| Concept | One-line Meaning |
|---|---|
| Escalation | Hand off to a human, based on clear rules |
| Escalation Style | Solve first, escalate if they insist |
| Handoff Note | Clean summary for the human |
| Confidence Spot-Checks | Check accuracy by category |

### 💡 One Simple Example
A support agent escalates a refund request only after the policy limit ($500) is exceeded — not because the customer sounded annoyed.

---

# CHAPTER 10: Handling Errors in Multi-Agent Systems

## 10.1 Types of Errors

| Type | Example | Fix |
|---|---|---|
| **Transient** | Timeout, network hiccup | Just retry |
| **Validation** | Missing required field | Fix and resend |
| **Business** | Breaks a company policy | Explain / offer alternative |
| **Permission** | Not allowed | Escalate to a human |

**In Simple Words:** Sort problems into: "try again later," "you filled the form wrong," "against the rules," or "you're not allowed."

---

## 10.2 Common Mistakes to Avoid

**Sample Output — bad vs. good:**
```
❌ BAD:  "search unavailable" (no useful detail)
✅ GOOD: {"failure_type": "timeout", "partial_results": [...], 
          "alternative": "try a narrower search"}
```

> **Rule:** Don't throw away all your work just because ONE small part failed — keep the parts that succeeded and flag the gap.

---

## 10.3 Reporting Partial Success

**In Simple Words:** A weather report that says "we couldn't get data from one station" is better than pretending the whole report failed.

**Sample Output:**
```markdown
### Music Section (PARTIAL COVERAGE — search agent timed out)
⚠️ Note: limited coverage due to a timeout.
```

---

### ✅ Key Points to Remember — Chapter 10
- Errors fall into 4 types: **transient, validation, business, permission**.
- Give **detailed, useful** error messages — not vague ones.
- Keep and report **partial success**, don't discard everything on one failure.

### 🔁 Quick Revision — Chapter 10
| Concept | One-line Meaning |
|---|---|
| Error Types | Transient / Validation / Business / Permission |
| Good Error Messages | Detailed, with retry guidance |
| Partial Success | Keep what worked, flag what didn't |

### 💡 One Simple Example
If a search across 5 sources fails for 1 source, the report still shows results from the other 4, with a clear note about the missing one.

---

# CHAPTER 11: Keeping Context Clean in Big Systems

## 11.1 The Facts Box

**Facts Box** is a small, always-updated block of key facts included in every message, so key details never get fuzzy after summarizing.

**In Simple Words:** Keeping sticky notes with the most important numbers on your monitor, even while your desk gets messier.

**Sample Output:**
```
=== CASE FACTS ===
Customer ID: CUST-12345
Order Amount: $89.99
Issue: Damaged item
===
```

---

## 11.2 Trimming Extra Info

**In Simple Words:** Only keep the fields you actually need from a tool's answer, and throw out the rest.

**Sample Output:**
```python
# A tool returned 40 fields, but you only keep 5:
{"order_id": ..., "status": ..., "total": ..., "items": ..., "return_eligible": ...}
```

---

## 11.3 Placement of Important Info

Refer to the **"Lost in the Middle"** rule (Chapter 1): put key findings at the top, supporting detail in the middle, and action items at the bottom.

---

## 11.4 Scratchpad Files

**Scratchpad File** is a file where Claude writes down what it has learned, so it doesn't have to re-discover the same things later.

**In Simple Words:** A detective's notebook — instead of re-interviewing every witness again tomorrow, they just read their notes.

**Sample Output:**
```markdown
# investigation-scratchpad.md
- refund() is called from 3 places
- External API has a rate limit of 100 req/min
```

---

## 11.5 Sending Small Jobs to Helpers

**In Simple Words:** Let a subagent dig through 15 files and report back just ONE summary line — so the main context stays clean.

**Sample Output:**
```
Main agent: "Investigate the payments module dependencies"
Subagent: reads 15 files
Subagent reports back: "Payments depends on AuthService, OrderModel, PaymentGateway"
→ Main agent keeps 1 line instead of 15 full files!
```

---

## 11.6 Saving Progress (Crash Recovery)

**In Simple Words:** Each agent writes down its own status, so if something crashes, you can see exactly where things stopped.

**Sample Output:**
```json
{"web-search": "completed", "doc-analysis": "in_progress", "synthesis": "not_started"}
```

---

### ✅ Key Points to Remember — Chapter 11
- Keep a **facts box** so key numbers never get lost or blurred.
- **Trim** tool results to only the fields you actually need.
- Place important info at the **start or end**, not the middle.
- Use **scratchpad files** so agents don't repeat past work.
- Delegate large digging tasks to subagents; keep only the summary.

### 🔁 Quick Revision — Chapter 11
| Concept | One-line Meaning |
|---|---|
| Facts Box | Always-updated key facts block |
| Trimming | Keep only needed fields |
| Scratchpad File | Notebook of learned facts |
| Delegation | Subagent digs, main agent gets a summary |

### 💡 One Simple Example
A "CASE FACTS" block at the top of every message keeps the refund amount ($89.99) accurate throughout a long conversation, even after several summarizations.

---

# CHAPTER 12: Always Show Your Sources (Provenance)

## 12.1 Track the Source

**Provenance** means always keeping track of **where** a fact came from, not just the fact itself.

**Sample Output:**
```
❌ Bad:  "The market is worth $3.2B." (Says who? When?)
✅ Good: {"claim": "...$3.2B", "source": "Global AI Music Report 2024", "date": "2024-06-15"}
```

---

## 12.2 When Sources Disagree

**In Simple Words:** Two witnesses give slightly different accounts — a good detective writes down BOTH versions, not just the one they prefer.

**Sample Output:**
```json
{
  "values": [
    {"value": "12%", "source": "Spotify Report", "date": "2024-03"},
    {"value": "8%", "source": "Industry Survey", "date": "2024-07"}
  ],
  "conflict_detected": true
}
```

---

## 12.3 Always Include Dates

```
❌ Confusing: "Source A says 10%, source B says 15%. Contradiction!"
✅ Clear:     "Source A (2023): 10%. Source B (2024): 15%. Looks like growth, not a contradiction."
```

---

## 12.4 Match Format to Content

| Content Type | Best Format |
|---|---|
| Numbers/money | Tables |
| News/explanations | Normal paragraphs |
| Step-by-step technical facts | Bullet lists |
| Timelines | Chronological order |

---

### ✅ Key Points to Remember — Chapter 12
- Always track **who** said a fact and **when**.
- Show **both values** when sources disagree — don't hide one.
- Adding **dates** can turn a "contradiction" into a clear trend.
- Match the **format** to the type of content being shown.

### 🔁 Quick Revision — Chapter 12
| Concept | One-line Meaning |
|---|---|
| Provenance | Track source + date for every fact |
| Conflicting Sources | Show both, don't hide either |
| Dates | Prevent false "contradictions" |

### 💡 One Simple Example
Instead of saying "Growth is 12%," a report says "Growth is 12% (Spotify Report, March 2024)" — this lets readers judge reliability themselves.

---

# CHAPTER 13: Claude Code's Built-in Toolbox

## 13.1 Choosing the Right Tool

**In Simple Words:** You wouldn't use a hammer to tighten a screw — each built-in tool has one clear job.

| I want to... | Use this Tool | Example |
|---|---|---|
| Find files by name | **Glob** | `**/*.test.tsx` |
| Search text inside files | **Grep** | find a function name |
| Read a whole file | **Read** | open and view it |
| Create a brand-new file | **Write** | make something from scratch |
| Change part of an existing file | **Edit** | swap out a specific snippet |
| Run a command | **Bash** | `npm test`, `git status` |

---

## 13.2 Explore Step-by-Step

**In Simple Words:** Build understanding piece by piece, instead of reading an entire codebase blindly.

**Sample Output — step-by-step exploration:**
1. **Grep**: find where `processPayment` is defined
2. **Read**: open that file
3. **Grep**: find everywhere it's called from
4. **Read**: open those files too

---

## 13.3 When Edit Doesn't Work

**In Simple Words:** If a text snippet appears more than once in a file, the **Edit** tool can get confused about which one to change.

**Fallback steps:**
1. **Read** the whole file.
2. Change it in memory.
3. **Write** the new version back.

---

### ✅ Key Points to Remember — Chapter 13
- Each built-in tool has **one clear job** — pick the right one.
- Explore codebases **step-by-step**, not all at once.
- If **Edit** fails on a repeated snippet, use **Read + Write** instead.

### 🔁 Quick Revision — Chapter 13
| Concept | One-line Meaning |
|---|---|
| Glob | Find files by name pattern |
| Grep | Search text inside files |
| Read/Write/Edit | Open / create / change files |
| Bash | Run commands |

### 💡 One Simple Example
To find a bug in a "processPayment" function, first use **Grep** to locate it, then **Read** the file, then **Edit** the specific broken line.

---

# CHAPTER 14: Quick Glossary (Exam Cheat Sheet)

**In Simple Words:** This is a fast lookup table of every key term, grouped by technology — great for last-minute revision.

## 14.1 Claude Agent SDK Terms

| Term | Simple Meaning |
|---|---|
| **AgentDefinition** | Blueprint: name + job + allowed tools for one agent |
| **Agent Loop** | Repeats until Claude signals it's fully done |
| **stop_reason** | Field showing why Claude stopped (`end_turn`, `tool_use`) |
| **Hooks (PostToolUse, PreToolUse)** | Code that runs automatically to check/clean/block actions |
| **Task tool** | Used by a coordinator to spawn/call a subagent |
| **allowedTools** | The list of tools an agent is permitted to use |

## 14.2 MCP Terms

| Term | Simple Meaning |
|---|---|
| **MCP Server** | Program offering tools/resources/prompts to Claude |
| **Tools** | Actions Claude can perform via MCP |
| **Resources** | Read-only data Claude can browse via MCP |
| **isError** | Flag marking a failed tool call |
| **.mcp.json** | Config file listing available MCP servers |

## 14.3 Claude Code Terms

| Term | Simple Meaning |
|---|---|
| **CLAUDE.md hierarchy** | Personal → team → folder-level instruction files |
| **.claude/rules/** | Conditional rules using glob patterns |
| **.claude/commands/** | Where slash commands live |
| **.claude/skills/ (SKILL.md)** | On-demand commands with tool restrictions |
| **Planning Mode** | Claude proposes a plan before acting |
| **/compact** | Shrinks conversation to save space |
| **--resume** | Continues a previous session |
| **fork_session** | Splits one session into independent branches |
| **-p / --print** | Non-interactive (headless) mode for CI/CD |

## 14.4 Claude API Terms

| Term | Simple Meaning |
|---|---|
| **tool_use** | Structured request/response format using JSON schemas |
| **tool_choice** | Controls if/which tool Claude must use (`auto`/`any`/forced) |
| **max_tokens** | Maximum length allowed for Claude's reply |
| **system prompt** | Instructions set before the conversation starts |

## 14.5 Other Important Terms

| Term | Simple Meaning |
|---|---|
| **Message Batches API** | 50% cheaper, up to 24-hour turnaround, uses `custom_id` |
| **JSON Schema** | Template enforcing required/optional/nullable fields, enums |
| **Pydantic** | A tool for validating schema data and catching semantic errors |
| **Few-shot Prompting** | Teaching by example instead of only description |
| **Prompt Chaining** | Splitting a big task into focused sequential steps |
| **Lost in the Middle** | Claude notices start/end of long text better than the middle |
| **Scratchpad File** | Notes file so agents don't repeat past discovery work |
| **Confidence Calibration** | Scoring how sure Claude is, checked against real accuracy |

### ✅ Key Points to Remember — Chapter 14
- This glossary maps every key term to its **one-line meaning** — use it for rapid-fire revision the night before the exam.
- Group your revision by **technology** (SDK, MCP, Claude Code, API) — exam questions are usually scenario-based but test these exact terms.

### 🔁 Quick Revision — Chapter 14
Scan the 5 tables above top to bottom — if you can explain each term in your own words without peeking, you're exam-ready for terminology.

### 💡 One Simple Example
If a question mentions "the coordinator calls a specialist agent to search the web," you should instantly recognize: **Task tool** + **subagent** + **Hub-and-Spoke pattern**.

---

# CHAPTER 15: What's NOT on the Exam (Out-of-Scope Topics)

**In Simple Words:** Knowing what you **don't** need to study is just as useful as knowing what you do — it saves your prep time.

**The following topics will NOT appear on the exam:**

- Fine-tuning Claude models or training custom models
- Claude API authentication, billing, or account management
- Deep implementation details in specific programming languages/frameworks
- Deploying or hosting MCP servers (infrastructure, networking, containers)
- Claude's internal architecture, training process, or model weights
- Constitutional AI, RLHF, or safety training methods
- Embedding models or vector database implementation details
- Computer use (browser automation, desktop interaction)
- Image analysis (Vision) capabilities
- Streaming API or server-sent events
- Rate limiting, quotas, or detailed API cost calculations
- OAuth, API key rotation, or authentication protocol details
- Cloud-provider-specific configuration (AWS, GCP, Azure)
- Performance benchmarks or model comparison metrics
- Prompt caching implementation details (just knowing it exists is enough)
- Token counting algorithms or tokenization specifics

### ✅ Key Points to Remember — Chapter 15
- The exam focuses on **architecture and decision-making**, not infrastructure or model internals.
- Don't waste time studying **billing, hosting, or authentication details**.
- A general awareness (not deep detail) is enough for things like prompt caching.

### 🔁 Quick Revision — Chapter 15
| Skip Studying... | Because... |
|---|---|
| Model training / fine-tuning | Not part of architecture decisions |
| Hosting/infra for MCP servers | Out of exam scope |
| Billing, auth, rate limits | Account-management details, not covered |

### 💡 One Simple Example
If a question asks about **how to reduce API costs using rate limits**, that's a trick — this exam does not test billing/rate-limit math, so double-check you're not overanalyzing an out-of-scope angle.

---

# CHAPTER 16: How to Prepare (Study Plan)

**In Simple Words:** These are hands-on practice steps — reading alone isn't enough, building small examples will make the concepts stick.

**Step-by-step preparation plan:**

1. **Build a real agent** with the Claude Agent SDK — implement a full agent loop with tool calling, error handling, and session management. Practice subagents and explicit context passing.

2. **Configure Claude Code for a real project** — set up the CLAUDE.md hierarchy, path-specific rules in `.claude/rules/`, skills with `context: fork` and `allowed-tools`, and MCP server integration.

3. **Design and test MCP tools** — write descriptions that clearly differentiate similar tools, return structured errors with categories and retry flags, and test against ambiguous requests.

4. **Build a data extraction pipeline** — use `tool_use` with JSON schemas, validation/retry loops, optional/nullable fields, and batch processing via the Message Batches API.

5. **Practice prompt engineering** — add few-shot examples for ambiguous scenarios, write explicit review criteria, and try multi-pass architectures for large code reviews.

6. **Study context management patterns** — practice extracting facts from verbose outputs, using scratchpad files, and delegating discovery work to subagents.

7. **Understand escalation and human-in-the-loop** — learn when to escalate (policy gaps, explicit user requests, inability to make progress) and how confidence-based routing works.

8. **Take a full practice exam** before the real one — it uses the same scenarios and format as the actual test.

### ✅ Key Points to Remember — Chapter 16
- **Hands-on building** beats passive reading for this exam.
- Focus extra practice time on **Domain 1 (Agents)**, since it's 27% of the exam.
- Always finish with a **practice exam** under real time conditions.

### 🔁 Quick Revision — Chapter 16
| Step | Focus Area |
|---|---|
| 1–2 | Agent SDK + Claude Code setup |
| 3–4 | MCP tools + structured data extraction |
| 5–6 | Prompt engineering + context management |
| 7–8 | Escalation + final practice exam |

### 💡 One Simple Example
Before the real exam, build a tiny 2-agent system (coordinator + 1 subagent) that searches for data and reports back a summary — this alone touches Domains 1, 2, and 5.

---

# 🎯 Final Recap — All Chapters

| Chapter | One-line Takeaway |
|---|---|
| 0. About the Exam | 5 domains, 720 to pass, no guessing penalty — study Domain 1 hardest (27%) |
| 1. API Basics | Always resend full history; Claude has no memory by itself |
| 2. Tools | Write crystal-clear tool descriptions; use schemas for structure |
| 3. Agents | Loop until `end_turn`; give subagents full context explicitly |
| 4. MCP | A universal plug for connecting outside tools/data |
| 5. Claude Code | CLAUDE.md = house rules; Skills = on-demand with tool limits; use planning mode for big changes |
| 6. Prompting | Show examples (few-shot); be specific, not vague |
| 7. Batches | Cheaper but slower — great for big, non-urgent jobs |
| 8. Decomposition | Break big tasks into small, focused steps |
| 9. Escalation | Escalate on clear rules, not "vibes" |
| 10. Errors | Keep partial results; give detailed, useful error messages |
| 11. Context | Keep a facts box; delegate big digging to subagents |
| 12. Provenance | Always track WHO said WHAT and WHEN |
| 13. Built-in Tools | Right tool for the right job; explore step-by-step |
| 14. Glossary | Quick lookup table of every key exam term |
| 15. Out-of-Scope | Skip billing, hosting, model internals — not tested |
| 16. Study Plan | Build small real examples; end with a full practice exam |

---

*These notes are written for beginners to build strong intuition first. Once comfortable, revisit the original detailed guide for exam-level depth and full practice questions.*
