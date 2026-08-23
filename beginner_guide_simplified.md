# Claude Certified Architect — Beginner's Simplified Guide

> This is a **simple, beginner-friendly** rewrite of the original study guide.
> Every topic is explained in plain language, using everyday analogies, followed by a
> **sample output** so you can see exactly what it looks like in practice.

---

## How to use this guide
Each topic below follows the same easy pattern:

- 🧠 **What it is** — one-line simple definition
- 🍕 **Think of it like...** — a real-world analogy
- ⚙️ **How it works** — simple steps
- 📋 **Sample Output** — a real example you can look at

---

# CHAPTER 1: Talking to Claude (The API Basics)

## 1.1 What is an "API request"?
🧠 **What it is:** A message you send to Claude, and the reply you get back.

🍕 **Think of it like:** Ordering food at a restaurant. You (the app) give the waiter (API) your order (the request). The kitchen (Claude) cooks it and the waiter brings back your food (the response).

⚙️ **How it works:**
1. You tell Claude which "model" (which chef) should cook
2. You tell it how long the reply can be (`max_tokens`)
3. You give instructions (`system` prompt = "recipe rules")
4. You send the conversation so far (`messages`)

📋 **Sample Output (what you send):**
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
📋 **Sample Output (what you get back):**
```json
{
  "role": "assistant",
  "content": [{"type": "text", "text": "Hello! How can I help you today?"}],
  "stop_reason": "end_turn"
}
```

**Important beginner tip:** Claude has no memory of past chats by itself — **you must resend the whole conversation every single time**, like re-reading the whole letter before replying to it.

---

## 1.2 Message Roles — Who is "talking"?
🧠 **What it is:** Every message has a label saying who sent it.

🍕 **Think of it like:** A group chat where each message shows the sender's name.

| Role | Who it is | Simple meaning |
|---|---|---|
| `user` | You / the customer | The question or the tool's answer |
| `assistant` | Claude | Claude's reply, or Claude asking to use a tool |
| `system` | The instructions | The "rules of the game" told once at the start |

📋 **Sample Output:**
```json
{"role": "user", "content": "What's the weather?"}
{"role": "assistant", "content": "Let me check that for you."}
```

**Beginner note:** Even a tool's answer is sent back as a `user` message (not a special "tool" role) — it's just wrapped as a `tool_result`.

---

## 1.3 `stop_reason` — Why did Claude stop talking?
🧠 **What it is:** A small note in the reply explaining why Claude stopped.

🍕 **Think of it like:** A waiter telling you "the chef is done cooking" vs "the chef needs another ingredient from you."

| Value | Simple meaning |
|---|---|
| `end_turn` | ✅ Claude is fully done — show the answer |
| `tool_use` | 🛠️ Claude wants to use a tool — go run it |
| `max_tokens` | ✂️ Claude got cut off — answer may be incomplete |

📋 **Sample Output:**
```json
{"stop_reason": "tool_use"}
```
This tells your code: "Stop and go run a tool now, then come back."

---

## 1.4 System Prompt — Setting the Rules
🧠 **What it is:** The instructions you give Claude before the conversation starts.

🍕 **Think of it like:** Giving a new employee their job description on day one.

📋 **Sample Output:**
```
system: "You are a customer support agent. Always be polite.
Verify the customer's identity before discussing their order."
```
⚠️ **Beginner warning:** Be careful with wording! Saying "always verify the customer" might make Claude call the verification tool even when it's not needed — like an overly cautious new employee.

---

## 1.5 Context Window — Claude's "Memory Desk"
🧠 **What it is:** The total amount of text Claude can "see" at once.

🍕 **Think of it like:** A desk where you can only fit so many papers. If you pile on too much, old papers fall off the edge (get forgotten).

⚙️ **3 common problems (beginner-simple):**
1. **Lost in the middle** — Claude notices things at the *start* and *end* of a long text better than the *middle*. (Put important stuff first or last!)
2. **Tool result clutter** — if a tool sends back 40 pieces of info but you need only 5, the extra 35 waste desk space.
3. **Fuzzy summaries** — when Claude compresses old chat, exact numbers ("$150.00") can turn vague ("around $150").

📋 **Sample Output (good vs bad placement):**
```
GOOD:
[KEY FACTS AT TOP] Customer wants a refund of $89.99
... (lots of detail in the middle) ...
[ACTION NEEDED AT BOTTOM] Approve refund

BAD:
... (customer wants refund of $89.99 buried on page 5 of 10) ...
```

---

# CHAPTER 2: Tools — Giving Claude "Hands"

## 2.1 What is `tool_use`?
🧠 **What it is:** A way for Claude to ask your program to *do something* (like check a database), since Claude itself cannot run code.

🍕 **Think of it like:** Claude is a manager who can't personally go to the warehouse — it asks an employee (your code) to go check stock and report back.

⚙️ **How it works (simple loop):**
```
1. Claude says: "I need to call get_customer"
2. Your code runs get_customer()
3. Your code sends the result back to Claude
4. Claude uses that result to keep answering
```

---

## 2.2 Defining a Tool — Writing the "Job Instructions"
🧠 **What it is:** A description telling Claude what a tool does and how to use it.

🍕 **Think of it like:** A menu description at a restaurant — the clearer it is, the less likely you order the wrong dish.

📋 **Sample Output (a well-written tool):**
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

**Beginner golden rule:** 📝 The **description is everything**. If two tools sound similar ("analyze_content" vs "analyze_document"), Claude will get confused — just like you would, reading two nearly identical menu items.

---

## 2.3 `tool_choice` — How much freedom does Claude have?
🧠 **What it is:** A setting that controls whether Claude *must* use a tool or *can choose* not to.

🍕 **Think of it like:** Telling an employee: "use your judgment" vs "you MUST fill out this form" vs "you MUST use THIS specific form."

| Setting | Simple meaning |
|---|---|
| `auto` | Claude decides — normal default |
| `any` | Claude MUST use *some* tool |
| `{"type": "tool", "name": "X"}` | Claude MUST use tool X specifically |

📋 **Sample Output:**
```json
"tool_choice": {"type": "tool", "name": "extract_metadata"}
```
This forces Claude to always start with `extract_metadata` first.

---

## 2.4 JSON Schemas — Getting Neat, Predictable Answers
🧠 **What it is:** A "form template" that forces Claude's answer into a clean, structured shape (instead of messy free text).

🍕 **Think of it like:** Giving someone a fill-in-the-blanks form instead of a blank sheet of paper — you always get the same fields back.

📋 **Sample Output:**
```json
{
  "category": "bug",
  "severity": "high",
  "confidence": 0.87,
  "category_detail": null
}
```

**3 beginner rules for building forms (schemas):**
1. Only mark a field "required" if it's ALWAYS available — otherwise Claude might make something up to fill it.
2. Allow `null` for optional info — so Claude can honestly say "I don't know" instead of guessing.
3. Add an `"other"` option to lists (enums) so nothing gets lost if it doesn't fit a category.

⚠️ **Beginner note:** A schema guarantees the *shape* is correct (no typos in the JSON) — it does **not** guarantee the *values* are correct. Claude could still put the wrong number in the right box!

---

## 2.5 Two Kinds of Mistakes
🧠 **What it is:** Errors come in two flavors.

| Type | Example | Simple fix |
|---|---|---|
| **Syntax error** | Broken JSON, missing bracket | Use a schema (`tool_use`) — this basically eliminates it |
| **Semantic error** | Numbers don't add up, wrong value | Double-check the answer / ask Claude to re-check |

---

# CHAPTER 3: Building "Agents" (Claude That Acts on Its Own)

## 3.1 The Agentic Loop — Claude's "To-Do Cycle"
🧠 **What it is:** A repeating cycle where Claude keeps working step-by-step until the job is really done.

🍕 **Think of it like:** A chef repeatedly tasting, adjusting, tasting again — until the dish is finally ready to serve.

⚙️ **Simple loop:**
```
Ask Claude → 
  If it says "I need a tool" → run the tool → tell Claude the result → ask again
  If it says "I'm done" (end_turn) → show the final answer
```

📋 **Sample Output (a mini loop trace):**
```
Turn 1: Claude → tool_use: lookup_order(id=123)
Turn 2: Your code → runs lookup_order, gets {"status": "shipped"}
Turn 3: Claude → "Your order has shipped!" (stop_reason: end_turn)
```

⚠️ **Beginner mistake to avoid:** Don't guess when Claude is "done" by scanning for the words "task completed" in the text, or by just stopping after 5 tries. The ONLY safe signal is `stop_reason == "end_turn"`.

---

## 3.2 `AgentDefinition` — Creating a Worker
🧠 **What it is:** A blueprint describing one agent's name, job, and allowed tools.

🍕 **Think of it like:** An employee's ID badge — it says their name, their department, and what doors they're allowed to open.

📋 **Sample Output:**
```python
agent = AgentDefinition(
    name="customer_support",
    system_prompt="You are a customer support agent...",
    allowed_tools=["get_customer", "lookup_order", "process_refund"]
)
```
**Beginner rule: Least privilege** — only give an agent the tools it truly needs, like only giving an intern the door keys for their own office, not the whole building.

---

## 3.3 Coordinator + Subagents (Hub and Spoke)
🧠 **What it is:** One "manager" agent that delegates smaller jobs to specialist "worker" agents.

🍕 **Think of it like:** A restaurant manager assigning tasks to the grill cook, the salad chef, and the dessert chef — each does their own part, then reports back to the manager.

```
         Coordinator (Manager)
        /         |          \
   Web Search   Document      Report
   Subagent     Analyzer      Writer
```

⚠️ **Very important beginner point:** Subagents have **no memory of the manager's conversation**. If you don't tell them the details directly, they know NOTHING — like hiring a temp worker who's never seen your files unless you hand them a folder.

📋 **Sample Output (bad vs good delegation):**
```
❌ BAD:  Task: "Analyze the document"
✅ GOOD: Task: "Analyze this document: [full text].
          Focus on financial risks. Return a JSON summary."
```

---

## 3.4 The `Task` Tool — Calling in a Specialist
🧠 **What it is:** The specific tool the manager agent uses to "hire" a subagent for a job.

🍕 **Think of it like:** Picking up the phone and calling a specialist consultant for a specific question.

📋 **Sample Output (running 3 helpers at once):**
```
Task 1: "Search for articles about AI music" 
Task 2: "Analyze document Y for key facts"
Task 3: "Search for articles about AI film"
→ All three run at the same time (parallel), saving time!
```

---

## 3.5 Hooks — Automatic Safety Checks
🧠 **What it is:** Code that runs automatically at key moments — either to clean up data, or to block risky actions.

🍕 **Think of it like:** A security guard at a door who ALWAYS checks ID, no matter how nicely you ask to skip it. Compare this to just *asking* people nicely to show ID (a prompt) — some might sneak past.

| | Hooks | Just asking in the prompt |
|---|---|---|
| Reliability | 100% guaranteed | Usually works, but not 100% |
| Use for | Money limits, legal rules, safety | General style preferences |

📋 **Sample Output (a safety hook):**
```python
@hook("PreToolUse")
def enforce_refund_limit(tool_call):
    if tool_call.name == "process_refund" and tool_call.args.amount > 500:
        return redirect_to_escalation(tool_call)
```
**Simple rule:** If breaking a rule could cost real money or cause real harm — use a **hook**, not just a polite instruction.

---

# CHAPTER 4: MCP (Model Context Protocol) — The Universal Plug

## 4.1 What is MCP?
🧠 **What it is:** A standard way to connect Claude to outside systems (like Jira, GitHub, or your own database).

🍕 **Think of it like:** A USB port — one standard plug shape that lets you connect all kinds of different devices.

MCP gives Claude 3 things:
1. **Tools** — actions it can perform (like "create ticket")
2. **Resources** — data it can read (like a list of all your files)
3. **Prompts** — ready-made question templates

---

## 4.2 & 4.3 MCP Servers — The "Plug-in Boxes"
🧠 **What it is:** A small program that plugs into Claude and offers a set of tools.

🍕 **Think of it like:** Installing an app on your phone — once installed, its features just show up and are ready to use.

📋 **Sample Output (`.mcp.json` — shared with the whole team):**
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

**Beginner tip:** 
- `.mcp.json` (project-level) = shared with your whole team
- `~/.claude.json` (personal) = just for you, for testing new things

---

## 4.4 `isError` — Telling Claude Something Went Wrong
🧠 **What it is:** A flag that tells Claude "this tool call failed" — plus (ideally) WHY it failed.

🍕 **Think of it like:** Instead of a store just saying "Sorry, error" — a good store says "Sorry, we're out of stock, but here's a similar item, and it'll be back Tuesday."

📋 **Sample Output (a GOOD, helpful error):**
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
📋 **Sample Output (a BAD, unhelpful error):**
```json
{"isError": true, "content": "Operation failed"}
```
The bad version leaves Claude guessing — should it retry? Give up? Tell the user? A good error message answers those questions in advance.

---

## 4.5 MCP Resources — The "Map" of Available Data
🧠 **What it is:** Read-only reference info Claude can look at, without needing to "do" anything.

🍕 **Think of it like:** A library's card catalog — you can browse what's available before deciding what to actually check out.

---

# CHAPTER 5: Claude Code — Your Coding Assistant Setup

## 5.1 CLAUDE.md — The Instruction Manual
🧠 **What it is:** A file where you write standing instructions for Claude Code to always follow.

🍕 **Think of it like:** A sticky note on an employee's desk with "house rules" they should always remember.

**3 levels (like nested folders of rules):**
```
1. ~/.claude/CLAUDE.md      → just for YOU (personal habits)
2. .claude/CLAUDE.md        → for the WHOLE TEAM (project rules)
3. subfolder/CLAUDE.md      → only for that specific folder
```

⚠️ **Common beginner mistake:** Putting team rules in your *personal* file (level 1) by accident — then new teammates never see them!

---

## 5.2 `@path` — Linking Files Together
🧠 **What it is:** A shortcut to pull in content from another file, instead of copy-pasting it.

🍕 **Think of it like:** Instead of retyping the same recipe in every cookbook, you just write "see page 42."

📋 **Sample Output:**
```markdown
Coding standards are described in @./standards/coding-style.md
```

---

## 5.3 `.claude/rules/` — Rules That Only Apply Sometimes
🧠 **What it is:** Separate rule files that only "turn on" when you're editing matching files.

🍕 **Think of it like:** A dress code that only applies in the kitchen (chef whites), not in the office.

📋 **Sample Output:**
```yaml
---
paths: ["**/*.test.tsx", "**/*.test.ts"]
---
Tests must use describe/it blocks.
Do not mock the database—use a test database.
```
**Why it's smart:** It saves space — Claude doesn't load "testing rules" while you're editing an unrelated file.

---

## 5.4 & 5.5 Slash Commands and Skills — Saved Shortcuts
🧠 **What it is:** A reusable command you type as `/name` that runs a pre-written instruction.

🍕 **Think of it like:** A speed-dial button on an old phone — instead of typing the whole task every time, you just press one shortcut.

📋 **Sample Output:**
```yaml
---
context: fork
allowed-tools: ["Read", "Grep", "Glob"]
---
Analyze the code structure in the specified directory.
```
Typing `/review` runs this instantly.

**Beginner cheat sheet:**
- `context: fork` = do this quietly in the background (doesn't clutter your main chat)
- `allowed-tools` = a safety leash — the skill can't do MORE than this list allows

---

## 5.6 Planning Mode vs Direct Execution
🧠 **What it is:** Should Claude *think first and ask permission*, or just *do it immediately*?

🍕 **Think of it like:** Big house renovation? Get a blueprint approved first (planning mode). Fixing a leaky faucet? Just fix it (direct execution).

| Use Planning Mode when... | Use Direct Execution when... |
|---|---|
| Big changes, many files | One small clear fix |
| Multiple possible approaches | The fix is obvious |
| Unfamiliar codebase | You already understand the code |

---

## 5.7 & 5.8 `/compact` and `/memory`
🧠 **What they are:**
- `/compact` = squeeze the conversation to save space (like zipping a folder) — but exact numbers can get fuzzy afterward.
- `/memory` = open the CLAUDE.md file to save permanent notes for next time.

🍕 **Think of it like:** `/compact` is cleaning your desk by summarizing old notes. `/memory` is writing something in your permanent notebook that you'll always bring with you.

---

## 5.9 Using Claude Code in CI/CD (Automated Pipelines)
🧠 **What it is:** Running Claude Code automatically, with no human watching, as part of automated software checks.

🍕 **Think of it like:** A vending machine instead of a cashier — no back-and-forth conversation, just: insert request, get result.

📋 **Sample Output:**
```bash
claude -p "Review this PR for security issues" --output-format json
```
`-p` (print mode) = run once, print the answer, and stop — perfect for robots/automation, since there's no one there to answer follow-up questions.

---

## 5.10 Resuming and Forking Sessions
🧠 **What it is:** Continuing a past conversation (`--resume`), or splitting one conversation into two independent paths (`fork_session`).

🍕 **Think of it like:**
- `--resume` = picking up a paused movie where you left off.
- `fork_session` = making two remakes of the same movie from the same starting scene, then having them go in different directions.

```
Investigation
     |
 fork_session
  /         \
Plan A:     Plan B:
Redux       Context API
```

---

# CHAPTER 6: Prompt Engineering — Talking to Claude Effectively

## 6.1 Few-shot Prompting — "Show, Don't Just Tell"
🧠 **What it is:** Giving Claude a couple of examples of input → correct output, instead of just describing the rule in words.

🍕 **Think of it like:** Teaching a kid to tie shoes — showing them 2-3 times works much better than just describing it in words.

📋 **Sample Output:**
```
Text: "about two handfuls of rice"
-> {"amount": "~100g", "precision": "approximate"}

Text: "a pinch of salt"
-> {"amount": "~1g", "precision": "approximate"}
```
Now Claude can guess new cases in the same style ("a dash of pepper" → similar pattern).

---

## 6.2 Clear Rules vs Vague Instructions
🧠 **What it is:** Being extremely specific about what counts as "good" or "bad," instead of using fuzzy words.

🍕 **Think of it like:** Telling a babysitter "put the kids to bed by 8:15pm, after teeth-brushing and one story" instead of just "put them to bed at a reasonable time."

📋 **Sample Output:**
```
❌ Vague: "Be conservative — report only high-confidence findings."

✅ Clear: Flag a comment ONLY if:
1. It contradicts the actual code
2. It references a function that doesn't exist
3. It's a leftover TODO for a bug already fixed
```

---

## 6.3 Prompt Chaining — Splitting Big Jobs into Small Steps
🧠 **What it is:** Breaking one huge task into several smaller, focused steps done one at a time.

🍕 **Think of it like:** Grading 10 exam papers ONE at a time carefully, instead of skimming all 10 at once and missing mistakes.

📋 **Sample Output:**
```
Step 1: Review auth.ts        → issues found in auth.ts
Step 2: Review database.ts    → issues found in database.ts
Step 3: Check how files connect → cross-file issues
```

---

## 6.4 The "Interview" Pattern — Ask Before You Build
🧠 **What it is:** Claude asks clarifying questions before diving into a solution.

🍕 **Think of it like:** A tailor measuring you and asking about your style before cutting fabric, instead of guessing your size.

📋 **Sample Output:**
```
Claude: "Before I build the caching system, quick questions:
1. Should the cache refresh automatically, or on a schedule?
2. Is it OK to show slightly old data if the cache is down?"
```

---

## 6.5 Validation and Retry — "Check Your Work"
🧠 **What it is:** Automatically checking Claude's output for mistakes, and asking it to fix them if something's wrong.

🍕 **Think of it like:** A teacher handing back a math test saying "Question 3 is wrong, try again" instead of just marking it wrong and moving on.

📋 **Sample Output:**
```
Error found: Field 'total' = 150, but sum(line_items) = 145.
→ Claude is asked to re-check and fix it.
```
**Beginner note:** This works great for math or format mistakes. It does NOT work if the information simply isn't in the document at all — Claude can't invent facts that were never provided.

---

## 6.6 Self-correction — Claude Double-Checking Itself
🧠 **What it is:** Asking Claude to calculate something itself, and compare it to the stated value, flagging any mismatch.

📋 **Sample Output:**
```json
{
  "stated_total": "$150.00",
  "calculated_total": "$145.00",
  "conflict_detected": true
}
```

---

# CHAPTER 7: Message Batches API — Bulk Processing (Save Money!)

## 7.1 What is it?
🧠 **What it is:** Instead of asking Claude one question and waiting instantly, you submit a big pile of questions and get all the answers later (up to 24 hours), for half the price.

🍕 **Think of it like:** Choosing "standard shipping" (cheaper, slower) instead of "next-day delivery" (pricier, instant) when you're not in a rush.

| | Batch API | Normal (Synchronous) API |
|---|---|---|
| Cost | 50% cheaper | Full price |
| Speed | Up to 24 hours | Instant |
| Best for | Big bulk jobs, not urgent | Live chats, urgent PR checks |

---

## 7.2 When to Use Which
📋 **Sample Output (decision table):**
```
✅ Batch:  Processing 10,000 old documents overnight
✅ Sync:   A customer waiting live for a chat reply
```

---

## 7.3 & 7.4 `custom_id` and Handling Failures
🧠 **What it is:** A label you attach to each request in a batch, so you can match it back to its answer later — and re-send just the failed ones.

📋 **Sample Output:**
```json
{"custom_id": "doc-invoice-2024-001", "params": {...}}
```
🍕 **Think of it like:** Writing your name on your exam paper before handing it into a huge stack — so it can be found again later.

---

## 7.5 Planning Around the 24-Hour Wait
📋 **Sample Output:**
```
Need results by: 30 hours from now
Batch takes up to: 24 hours
→ You must submit the batch within the first 6 hours!
```

---

# CHAPTER 8: Breaking Down Big Tasks

## 8.1 Fixed Pipelines
🧠 **What it is:** A task with steps that never change, done the same way every time.

🍕 **Think of it like:** An assembly line — always Step A, then B, then C, in the same order.

```
Document → Extract metadata → Extract data → Validate → Final output
```
Good for: predictable, repeatable jobs (e.g., always reviewing invoices the same way).

---

## 8.2 Dynamic (Flexible) Decomposition
🧠 **What it is:** The next step is decided AFTER seeing what was found, not planned in advance.

🍕 **Think of it like:** A detective — you don't know your next move until you see the clues from your last move.

📋 **Sample Output:**
```
1. "Add tests for old code"
2. → Found: 3 modules with no tests at all
3. → Decide: start with the riskiest one (payments)
4. → Found mid-way: it depends on an outside API
5. → Adapt: add a fake (mock) version of that API first
```

---

## 8.3 Multi-pass Code Review
🧠 **What it is:** Reviewing files one-by-one carefully first, THEN checking how they connect, instead of eyeballing everything at once.

🍕 **Think of it like:** Proofreading each chapter of a book separately before checking if the whole story makes sense together.

---

# CHAPTER 9: When to Ask a Human for Help (Escalation)

## 9.1 Clear Reasons to Escalate
🧠 **What it is:** Specific, reliable situations where Claude should hand off to a real person.

🍕 **Think of it like:** A cashier calling the manager over — not because they "feel like it," but because of a clear store rule (e.g., "refunds over $500 need manager approval").

| Situation | What to do |
|---|---|
| Customer says "get me a manager" | Escalate immediately |
| The rulebook (policy) doesn't cover this case | Escalate |
| Big money is involved | Escalate (ideally auto-blocked by a hook) |

**Beginner warning — unreliable escalation triggers:**
❌ "The customer sounds upset" (mood ≠ actual complexity)
❌ "Claude rated its own confidence as low" (Claude can be confidently WRONG)

---

## 9.2 Escalation Style — Be Kind First
📋 **Sample Output:**
```
Customer: "This is outrageous, I'm unhappy with the quality!"
Agent: "I understand your frustration. I can offer a replacement or refund."
Customer: "No, I want to talk to someone!"
Agent: [NOW escalates — because they insisted again]
```
🍕 **Simple rule:** Acknowledge feelings → offer a real solution → only escalate if they insist. Don't panic-escalate on the FIRST sign of unhappiness.

---

## 9.3 The Handoff Note — Helping the Human Take Over
🧠 **What it is:** A clean summary Claude writes for the human, since the human hasn't read the whole conversation.

🍕 **Think of it like:** A nurse handing a doctor a clipboard summary of the patient, instead of the doctor reading the whole hospital chat log.

📋 **Sample Output:**
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
🧠 **What it is:** Claude rates how sure it is about each answer, and humans spot-check a sample — even the "confident" ones — to catch hidden mistakes.

🍕 **Think of it like:** A factory that still randomly tests "perfect-looking" products, because looks can be deceiving.

📋 **Sample Output:**
```
Overall accuracy: 97% (looks great!)
But for "handwritten invoices" specifically: only 60% accurate
→ Need to check accuracy BY CATEGORY, not just overall.
```

---

# CHAPTER 10: Handling Errors in Multi-Agent Systems

## 10.1 Types of Errors
🍕 **Think of it like:** Sorting problems into: "try again later" vs "you filled the form wrong" vs "against the rules" vs "you're not allowed."

| Type | Example | Fix |
|---|---|---|
| Transient | Timeout, network hiccup | Just retry |
| Validation | Missing required field | Fix and resend |
| Business | Breaks a company policy | Explain / offer alternative |
| Permission | Not allowed | Escalate to a human |

---

## 10.2 Common Mistakes to Avoid
📋 **Sample Output (bad vs good):**
```
❌ BAD:  "search unavailable" (no useful detail)
✅ GOOD: {"failure_type": "timeout", "partial_results": [...], 
          "alternative": "try a narrower search"}
```
🧠 **Rule:** Don't throw away all your work just because ONE small part failed — keep the parts that succeeded and just flag the gap.

---

## 10.3 & 10.4 Reporting Partial Success
📋 **Sample Output:**
```markdown
### Music Section (PARTIAL COVERAGE — search agent timed out)
⚠️ Note: limited coverage due to a timeout.
```
🍕 **Think of it like:** A weather report that says "we couldn't get data from one station" instead of pretending the whole report failed.

---

# CHAPTER 11: Keeping Context Clean in Big Systems

## 11.1 A "Facts Box" You Never Lose
🧠 **What it is:** A small, always-updated block of key facts included in every message, so key details never get fuzzy after summarizing.

📋 **Sample Output:**
```
=== CASE FACTS ===
Customer ID: CUST-12345
Order Amount: $89.99
Issue: Damaged item
===
```
🍕 **Think of it like:** Keeping sticky notes with the most important numbers on your monitor, even while your desk gets messier.

---

## 11.2 Trimming Extra Info
🧠 **What it is:** Only keeping the fields you actually need from a tool's answer, and throwing out the rest.

📋 **Sample Output:**
```python
# A tool returned 40 fields, but you only keep 5:
{"order_id": ..., "status": ..., "total": ..., "items": ..., "return_eligible": ...}
```

---

## 11.3 Put Important Info First or Last
Refer back to the "lost in the middle" idea from Chapter 1 — put key findings at the top, details in the middle, action items at the bottom.

---

## 11.4 Scratchpad Files — Claude's Notebook
🧠 **What it is:** A file where Claude writes down what it has learned, so it doesn't have to re-discover the same things later.

🍕 **Think of it like:** A detective's notebook — instead of re-interviewing every witness again tomorrow, they just read their notes.

📋 **Sample Output:**
```markdown
# investigation-scratchpad.md
- refund() is called from 3 places
- External API has a rate limit of 100 req/min
```

---

## 11.5 Sending Small Jobs to Helpers
🧠 **What it is:** Letting a subagent dig through 15 files and report back just ONE summary line — so your main context stays clean.

📋 **Sample Output:**
```
Main agent: "Investigate the payments module dependencies"
Subagent: reads 15 files
Subagent reports back: "Payments depends on AuthService, OrderModel, PaymentGateway"
→ Main agent keeps 1 line instead of 15 full files!
```

---

## 11.6 Saving Progress (Crash Recovery)
🧠 **What it is:** Each agent writes down its own status, so if something crashes, you can see exactly where things stopped.

📋 **Sample Output:**
```json
{"web-search": "completed", "doc-analysis": "in_progress", "synthesis": "not_started"}
```

---

# CHAPTER 12: Always Show Your Sources (Provenance)

## 12.1 Don't Lose the "Who Said This"
🧠 **What it is:** Always keep track of WHERE a fact came from, not just the fact itself.

📋 **Sample Output:**
```
❌ Bad:  "The market is worth $3.2B." (Says who? When?)
✅ Good: {"claim": "...$3.2B", "source": "Global AI Music Report 2024", "date": "2024-06-15"}
```

---

## 12.2 When Two Sources Disagree
🧠 **What it is:** Don't just pick one value and hide the other — show both, with their sources.

📋 **Sample Output:**
```json
{
  "values": [
    {"value": "12%", "source": "Spotify Report", "date": "2024-03"},
    {"value": "8%", "source": "Industry Survey", "date": "2024-07"}
  ],
  "conflict_detected": true
}
```
🍕 **Think of it like:** Two witnesses giving slightly different accounts — a good detective writes down BOTH versions, not just the one they like better.

---

## 12.3 Always Include Dates
```
❌ Confusing: "Source A says 10%, source B says 15%. Contradiction!"
✅ Clear:     "Source A (2023): 10%. Source B (2024): 15%. Looks like growth, not a contradiction."
```

---

## 12.4 Match the Format to the Content
- Numbers/money → tables
- News/explanations → normal paragraphs
- Step-by-step technical facts → bullet lists
- Timelines → chronological order

---

# CHAPTER 13: Claude Code's Built-in Toolbox

## 13.1 Which Tool for Which Job?
🍕 **Think of it like a toolbox:** you wouldn't use a hammer to tighten a screw.

| I want to... | Use this tool | Example |
|---|---|---|
| Find files by name | **Glob** | `**/*.test.tsx` |
| Search text INSIDE files | **Grep** | find a function name |
| Read a whole file | **Read** | open and view it |
| Create a brand-new file | **Write** | make something from scratch |
| Change part of an existing file | **Edit** | swap out a specific snippet |
| Run a command | **Bash** | `npm test`, `git status` |

---

## 13.2 Explore Step-by-Step, Not All at Once
📋 **Sample Output:**
```
1. Grep: find where "processPayment" is defined
2. Read: open that file
3. Grep: find everywhere it's called from
4. Read: open those files too
→ Build understanding piece by piece, instead of reading the entire codebase blindly.
```

---

## 13.3 When "Edit" Doesn't Work
🧠 **What it is:** If a text snippet appears more than once in a file, "Edit" can get confused about which one to change. The fallback: Read the whole file, change it in memory, then Write the new version back.

---

# 🎯 Quick Recap Cheat-Sheet

| Chapter | One-line takeaway |
|---|---|
| 1. API Basics | Always resend full history; Claude has no memory by itself |
| 2. Tools | Write crystal-clear tool descriptions; use schemas for structure |
| 3. Agents | Loop until `end_turn`; give subagents full context explicitly |
| 4. MCP | A universal plug for connecting outside tools/data |
| 5. Claude Code | CLAUDE.md = house rules; use planning mode for big changes |
| 6. Prompting | Show examples (few-shot); be specific, not vague |
| 7. Batches | Cheaper but slower — great for big, non-urgent jobs |
| 8. Decomposition | Break big tasks into small, focused steps |
| 9. Escalation | Escalate on clear rules, not "vibes" |
| 10. Errors | Keep partial results; give detailed, useful error messages |
| 11. Context | Keep a facts box; delegate big digging to subagents |
| 12. Provenance | Always track WHO said WHAT and WHEN |
| 13. Built-in Tools | Right tool for the right job; explore step-by-step |

---

*This simplified guide is meant as a beginner-friendly companion to the original detailed study guide — use it to build intuition first, then go back to the original for exam-level depth and the full practice questions.*
