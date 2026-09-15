# Domain 1 — Task 1.1: Agentic Loops

Yes — this is **Task 1.1: Agentic Loops**, and since you asked for it earlier, here is a cleaner **exam-notes version**, closely aligned with the page. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-1-agentic-loops))

# Domain 1 — Task 1.1: Agentic Loops

## 1. What is an Agentic Loop? ⭐⭐⭐

An **agentic loop** is the execution cycle that allows Claude to:

> **Think → use tools → receive results → think again → finish**

It is controlled by **code**, not by a prompt instruction or a normal chatbot conversation. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-1-agentic-loops))

---

# 2. The 4-Step Agentic Loop ⭐⭐⭐

Memorise this sequence:

```text
1. Send request to Claude
          ↓
2. Check stop_reason
          ↓
     ┌────┴────┐
     ↓         ↓
 tool_use   end_turn
     ↓         ↓
 Execute     Finish
 tool        agent
     ↓
 Add result to history
     ↓
 Send history back
     ↓
     LOOP
```

### Step 1 — Send request

Send a request through the **Messages API**.

The request includes the conversation history:

- System prompt
- Previous messages
- Previous tool results

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-1-agentic-loops))

---

### Step 2 — Check `stop_reason`

This is the **authoritative signal** for controlling the loop.

Two exam values:

| `stop_reason` | Meaning | Action |
|---|---|---|
| `"tool_use"` | Claude wants to use a tool | **Continue** |
| `"end_turn"` | Claude has finished | **Stop** |

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-1-agentic-loops))

### Golden rule 🧠

> **Never decide completion from Claude's text. Check `stop_reason`.**

---

# 3. When `stop_reason = "tool_use"` ⭐⭐⭐

Claude wants a tool to be executed.

Your application must:

1. Identify the requested tool.
2. Execute the tool.
3. Get the tool result.
4. Append the result to the conversation history.
5. Send the updated conversation back to Claude.
6. Check `stop_reason` again.

```text
Claude
  ↓
tool_use
  ↓
Execute tool
  ↓
Tool result
  ↓
Append to history
  ↓
Claude again
  ↓
Check stop_reason
```

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-1-agentic-loops))

---

# 4. Why Tool Results Must Be Added to History ⭐⭐⭐

This is a **major exam point**.

Claude is not automatically aware of what your application did with the tool.

So:

```text
Tool executes
      ↓
Result
      ↓
Add result to conversation
      ↓
Claude receives it
      ↓
Claude can reason about it
```

If you **don't append the tool result**, Claude doesn't see the new information and cannot properly continue its reasoning. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-1-agentic-loops))

### Simple example

User:

> "What is 25 × 4?"

Claude:

```text
tool_use → calculator(25 * 4)
```

Calculator:

```text
100
```

You must send that result back in the conversation.

Then Claude can respond:

> "The answer is 100."

---

# 5. When `stop_reason = "end_turn"` ⭐⭐⭐

This means:

> **Claude has finished its work.**

The application should:

```text
end_turn
   ↓
Exit loop
   ↓
Return final response
```

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-1-agentic-loops))

---

# 6. Model-Driven Decision-Making ⭐⭐⭐

Claude decides **which tool to use based on the current context**.

It does not have to follow a fixed sequence such as:

```text
Tool A
 ↓
Tool B
 ↓
Tool C
```

Instead:

```text
Current context
      ↓
Claude reasons
      ↓
Chooses appropriate tool
```

This is called:

> **Model-driven decision-making**

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-1-agentic-loops))

### Why is this useful?

Claude can adapt when:

- The situation changes
- New information appears
- A different tool becomes appropriate
- An unexpected case occurs

---

# 7. Important Exception — Business Logic ⚠️

Model-driven decisions are flexible, but **critical business rules should be enforced programmatically**.

Examples:

- Financial operations
- Security checks
- Regulatory requirements

For these:

```text
Claude decision
      ↓
Programmatic enforcement
      ↓
Allow / Block
```

So remember:

> **Flexible decisions → Claude**  
> **Critical mandatory rules → Code enforcement** ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-1-agentic-loops))

---

# 8. Three Anti-Patterns ⭐⭐⭐

These are **very important for the exam**.

## Anti-Pattern 1 — Parse Natural Language ❌

Bad:

```python
if "I'm done" in response:
    break
```

Why wrong?

Claude's natural language is **ambiguous**.

For example:

> "I've finished analysing the first file."

That doesn't necessarily mean the **whole task** is finished.

### Correct:

```text
Check stop_reason
```

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-1-agentic-loops))

---

# 9. Anti-Pattern 2 — Arbitrary Iteration Cap ❌

Bad:

```python
if iterations >= 10:
    break
```

Why wrong?

Maybe the task needs:

```text
12 iterations
```

Your application would stop too early.

Or maybe the task finishes after:

```text
3 iterations
```

Then continuing towards 10 is unnecessary.

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-1-agentic-loops))

### But are iteration caps completely forbidden?

**No.**

They are acceptable as a **safety net**.

For example:

```text
Normal control:
stop_reason

Safety:
MAX_ITERATIONS = 20
```

So:

> **Cap = safety mechanism, NOT primary termination mechanism.**

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-1-agentic-loops))

---

# 10. Anti-Pattern 3 — Check Text Content ❌

Bad:

```python
if response.content[0].type == "text":
    break
```

This is a classic exam trap.

Claude can return:

```text
Text
+
tool_use
```

in the **same response**.

Example:

```text
"I'll check the order for you."
        +
lookup_order(...)
```

If you see the text and immediately stop, you terminate the agent **before the tool executes**.

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-1-agentic-loops))

### Correct:

```text
stop_reason == "tool_use"
       ↓
Continue

stop_reason == "end_turn"
       ↓
Stop
```

---

# 11. `tool_choice = "any"` Trap 🚨

A developer might think:

> "I'll force Claude to always use a tool so it doesn't return text."

They use:

```text
tool_choice = "any"
```

❌ This is dangerous.

If Claude has genuinely finished, forcing it to select a tool can cause it to **keep calling tools**, potentially creating an **infinite loop**.

The correct approach is:

> Let Claude signal completion through `stop_reason`.

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-1-agentic-loops))

---

# 12. Classic Exam Bug 🎯

### Incorrect code

```python
response = client.messages.create(...)

if response.content[0].type == "text":
    return response
```

### What happens?

Claude returns:

```text
Text:
"I'll look up your order."

Tool:
lookup_order(...)
```

The application sees:

```text
content[0].type == "text"
```

and stops.

### Result:

❌ Incomplete answer.

### Correct fix:

```text
stop_reason
```

If:

```text
tool_use → execute tool and continue
end_turn → finish
```

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-1-agentic-loops))

---

# 13. Complete Mental Model 🧠

```text
             USER REQUEST
                  ↓
          Messages API
                  ↓
               CLAUDE
                  ↓
           stop_reason?
            /          \
           /            \
     tool_use          end_turn
        ↓                  ↓
   Execute tool          FINISH
        ↓
   Tool result
        ↓
 Append to history
        ↓
   Messages API
        ↓
      CLAUDE
        ↓
    repeat...
```

---

# 14. Exam Comparison Table

| Approach | Correct? | Why |
|---|---:|---|
| Check `stop_reason` | ✅ | Deterministic loop control |
| `"tool_use"` → continue | ✅ | Claude needs a tool |
| `"end_turn"` → stop | ✅ | Claude has finished |
| Append tool result to history | ✅ | Claude needs the new information |
| Parse `"I'm done"` | ❌ | Natural language is ambiguous |
| Check `content[0].type == "text"` | ❌ | Text can appear with `tool_use` |
| Stop after 10 iterations | ❌ | Can stop too early |
| Iteration cap as safety net | ✅ | Protects against runaway loops |
| `tool_choice="any"` to force completion | ❌ | Can create infinite tool calls |
| Model chooses tools from context | ✅ | Model-driven decision-making |

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-1-agentic-loops))

---

# 🎯 30-Second Exam Revision

Remember these **7 rules**:

1. **Messages API → send conversation history.**
2. **Check `stop_reason`.**
3. **`tool_use` → execute tool.**
4. **Append tool result → send history back.**
5. **`end_turn` → finish.**
6. **Never use text/natural language to determine completion.**
7. **Iteration caps are only safety nets.**

### The most important sentence:

> **`stop_reason` controls the loop — not Claude's text.** ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-1-agentic-loops))

One additional detail from the current guide: the live API has other `stop_reason` values beyond the two exam-focused ones. For the **Foundations exam**, the guide specifically focuses on `"tool_use"` and `"end_turn"`; in production, other values need explicit handling rather than blindly treating everything as `"tool_use"`. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-1-agentic-loops))

---

# Domain 1 — Task 1.2: Multi-Agent Orchestration

Here are **simple, exam-focused notes** for **Domain 1 → Task 1.2: Multi-Agent Orchestration**, based on the page you shared. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-2-orchestration-patterns))

# 1.2 Multi-Agent Orchestration — Simple Notes

## 1. What is Multi-Agent Orchestration?

**Multi-agent orchestration** = getting multiple Claude agents to work together on one complex task.

The exam focuses mainly on:

> **Hub-and-Spoke architecture**

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-2-orchestration-patterns))

---

# 2. Hub-and-Spoke Architecture ⭐⭐⭐

Think of it like this:

```text
              Subagent A
                  ↑
                  |
Subagent B ← Coordinator → Subagent C
                  |
                  ↓
              Subagent D
```

### Coordinator = Hub

The **coordinator** is the central agent.

It:

- Receives the main task
- Breaks the task into smaller tasks
- Decides which subagents to use
- Passes context to subagents
- Collects their results
- Handles errors
- Routes information between subagents

### Subagents = Spokes

Each subagent performs a **specialised task**.

Examples:

- Web search
- Document analysis
- Research
- Synthesis
- Report generation

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-2-orchestration-patterns))

---

# 3. Most Important Rule ⭐⭐⭐

> **All communication goes through the coordinator.**

Subagents **do not directly communicate with each other** for the purposes of the exam.

```text
Wrong ❌

Subagent A ─────→ Subagent B


Correct ✅

Subagent A ─→ Coordinator ─→ Subagent B
```

Why?

The coordinator provides:

1. **Observability** — everything can be monitored/logged centrally.
2. **Consistent error handling** — errors can be handled uniformly.
3. **Controlled information flow** — coordinator decides what information each agent receives.

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-2-orchestration-patterns))

### Exam memory trick:

> **Agent → Coordinator → Agent**

Never:

> **Agent → Agent**

---

# 4. Critical Isolation Principle ⭐⭐⭐

This is a **major exam trap**.

Subagents **do NOT automatically inherit** the coordinator's context.

A subagent does not automatically receive:

- Coordinator's system prompt
- Previous coordinator messages
- Results from other subagents
- Shared memory/global state

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-2-orchestration-patterns))

### Therefore:

The coordinator must **explicitly pass the required context**.

Example:

```text
Coordinator
     |
     | "Research solar energy efficiency.
     |  Here is the relevant context..."
     ↓
Web Search Agent
```

The subagent only knows what the coordinator gives it.

---

# 5. Subagents Don't Share Memory

Suppose:

```text
Coordinator → Search Agent
```

The search agent produces results.

Later:

```text
Coordinator → Search Agent
```

The second invocation **doesn't automatically remember the first invocation**.

Each invocation is independent.

Therefore:

> **Context must be explicitly passed every time it is needed.**

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-2-orchestration-patterns))

---

# 6. Coordinator's 4 Main Responsibilities ⭐

Remember:

### **S → P → R → C**

**S** = Selection  
**P** = Partition  
**R** = Refinement  
**C** = Communication

### 1. Dynamic Subagent Selection

The coordinator decides **which subagents are actually needed**.

It should NOT automatically invoke every subagent.

Example:

Simple question:

```text
User
 ↓
Coordinator
 ↓
Web Search Agent
```

No need to involve every available agent.

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-2-orchestration-patterns))

---

### 2. Research Scope Partitioning

When multiple agents are needed, give them **different areas of work**.

Example:

```text
Coordinator
    ├── Agent A → Academic papers
    ├── Agent B → News articles
    └── Agent C → Industry reports
```

This reduces duplicate work.

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-2-orchestration-patterns))

---

### 3. Iterative Refinement ⭐

The coordinator should check whether the results are complete.

If something is missing:

```text
Research
   ↓
Coordinator checks results
   ↓
Something missing?
   ↓
Yes
   ↓
Delegate targeted research
   ↓
Evaluate again
```

So orchestration isn't necessarily:

```text
Delegate → Done
```

It can be:

```text
Delegate → Evaluate → Find gaps → Delegate again → Evaluate
```

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-2-orchestration-patterns))

---

### 4. Centralised Communication Routing

All communication between subagents goes through the coordinator.

```text
Agent A
   ↓
Coordinator
   ↓
Agent B
```

This maintains:

- Observability
- Error handling
- Information control

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-2-orchestration-patterns))

---

# 7. Narrow Decomposition Failure ⭐⭐⭐

This is an important **exam scenario**.

Suppose the user asks:

> "What is the impact of AI on creative industries?"

But the coordinator only creates:

```text
Visual arts
Visual arts
Visual arts
```

It forgets:

- Music
- Writing
- Film

The subagents may perform their assigned tasks perfectly.

Yet the final answer is incomplete.

### Root cause?

❌ Not the search agent  
❌ Not the synthesis agent  
❌ Not lack of subagents  

✅ **Coordinator's task decomposition was too narrow.**

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-2-orchestration-patterns))

---

# 8. Example: Renewable Energy ⭐

User asks:

> "Research renewable energy technologies."

Bad decomposition:

```text
Coordinator
   ├── Solar
   └── Wind
```

Result:

```text
Solar ✅
Wind ✅
Geothermal ❌
Tidal ❌
Biomass ❌
Fusion ❌
```

The subagents may have researched **solar and wind perfectly**.

The problem is still:

> **Coordinator failed to cover the full scope.**

Correct decomposition should cover the breadth of the subject.

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-2-orchestration-patterns))

---

# 9. Exam Traps 🚨

### Trap 1 — Blame the subagent

If the final report misses entire categories:

> "The search agent must have done a bad job."

❌ Wrong.

First check:

> **Did the coordinator assign the missing topics?**

---

### Trap 2 — Assume shared memory

> "The subagent already knows what the coordinator discussed earlier."

❌ Wrong.

Subagents have **isolated context**.

---

### Trap 3 — Direct agent-to-agent communication

> "Let Agent A directly send its results to Agent B because it's faster."

❌ Wrong for the exam's hub-and-spoke model.

Use:

```text
A → Coordinator → B
```

---

### Trap 4 — Add more agents

If decomposition is:

```text
Solar + Wind
```

adding 10 more agents doesn't automatically fix it.

You still have:

```text
Solar + Wind
```

The problem is the **coordinator's decomposition**.

---

# 10. Super-Short Revision Sheet 🧠

```text
MULTI-AGENT ORCHESTRATION
          ↓
    HUB-AND-SPOKE
          ↓
     COORDINATOR
          ↓
 ┌────────┼────────┐
 ↓        ↓        ↓
Agent A  Agent B  Agent C
```

### Coordinator

**Receives → Decomposes → Selects → Delegates → Aggregates → Evaluates → Refines**

### Remember

| Concept | Exam answer |
|---|---|
| Architecture | Hub-and-spoke |
| Centre | Coordinator |
| Workers | Subagents |
| Communication | Through coordinator |
| Shared context? | ❌ No automatic sharing |
| Context passing | Explicit |
| Subagent selection | Dynamic |
| Research | Partition to avoid duplication |
| Incomplete result | Check coordinator decomposition |
| Missing categories | Narrow decomposition |
| Gaps found | Iterative refinement |

### One-line memory trick

> **Coordinator thinks about the whole task; subagents perform specialised pieces; context must be explicitly passed; communication goes back through the coordinator.** ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-2-orchestration-patterns))

If you're studying this for the certification, the **three biggest things to memorise are:**

**1. Hub-and-spoke**  
**2. Subagents are isolated — explicitly pass context**  
**3. Scope/coverage failures usually originate in coordinator decomposition**

---

# 1.3 Subagent Invocation & Context Passing

Here are **simple, exam-focused notes** for **Domain 1 → Task 1.3: Subagent Invocation & Context Passing**. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-3-subagent-invocation-context))

# 1. Task Tool ⭐⭐⭐

The **Task tool** is how the coordinator **spawns subagents**.

> Exam wording: **Task tool**

In current Claude Code, it is also called **Agent**, but for the exam, remember **Task tool**. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-3-subagent-invocation-context))

### Critical requirement

The coordinator's `allowedTools` must include:

```text
"Task"
```

or the current:

```text
"Agent"
```

Without this, the coordinator **cannot spawn subagents**. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-3-subagent-invocation-context))

### Easy memory

> **No Task/Agent in allowedTools → No subagents.**

---

# 2. AgentDefinition

Each subagent has **3 important things**:

```text
AgentDefinition
      │
      ├── Description
      ├── System prompt
      └── Tool restrictions
```

### Description
Tells the coordinator **what the subagent does**.

### System prompt
Tells the subagent **how to perform its role**.

### Tool restrictions
Defines **which tools that subagent can use**.

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-3-subagent-invocation-context))

---

# 3. Context Passing ⭐⭐⭐

This is probably the **most important concept in 1.3**.

### Key rule:

> **Subagents have isolated context.**

They do **not automatically receive**:

- Coordinator's conversation history
- Other subagents' results
- Previous context

The coordinator must **explicitly pass the information** in the subagent's prompt. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-3-subagent-invocation-context))

### Think of it like this:

```text
Coordinator
    │
    │ explicitly passes context
    ↓
Subagent
```

NOT:

```text
Coordinator
    │
    ↓
Subagent
    │
    └── magically knows everything ❌
```

---

# 4. Pass Complete Findings ⭐⭐⭐

Suppose you have:

```text
Web Search Agent
        ↓
Research results
```

and:

```text
Document Agent
        ↓
Document analysis
```

Now the coordinator invokes a **Synthesis Agent**.

The coordinator must pass **both complete results**.

```text
Web results ────────┐
                    ↓
Document results → Coordinator → Synthesis Agent
```

Don't assume the synthesis agent can retrieve the previous agents' results itself. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-3-subagent-invocation-context))

---

# 5. Structured Metadata ⭐⭐⭐

This is a **major exam trap**.

Don't pass only:

```text
"The solar industry grew significantly."
```

Pass the **content + metadata**:

```text
Claim:
"The solar industry grew significantly."

Source URL:
https://example.com

Document:
Annual Solar Report

Page:
14

Confidence:
High
```

The metadata allows the downstream agent to **attribute claims to their sources**. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-3-subagent-invocation-context))

### Easy memory:

> **Content tells WHAT. Metadata tells WHERE it came from.**

---

# 6. Exam Scenario: Missing Citations 🚨

Suppose:

- Web agent correctly returns URLs.
- Document agent correctly returns page numbers.
- Synthesis agent produces a report with **no citations**.

What went wrong?

The likely problem is:

> ❌ The coordinator passed the content but **stripped the metadata**.

It is **not necessarily a problem with the synthesis agent**. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-3-subagent-invocation-context))

### Correct fix

Pass:

```text
Claim
+
Source URL
+
Document name
+
Page number
+
Other relevant metadata
```

to the synthesis agent.

---

# 7. Goal-Oriented Prompts ⭐

When the coordinator invokes a subagent, specify:

> **WHAT you want it to achieve**

rather than:

> **exactly HOW it must perform every step**

### Better:

```text
"Find reliable evidence about solar efficiency
and provide source attribution."
```

### Less desirable:

```text
"First search website A,
then search website B,
then extract paragraph 3,
then..."
```

Goal-oriented instructions give the subagent flexibility to adapt to unexpected situations. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-3-subagent-invocation-context))

---

# 8. Parallel Subagent Invocation ⭐⭐⭐

Suppose you need:

```text
Agent A → Web research
Agent B → Document analysis
```

and the two tasks are **independent**.

Don't do:

```text
Coordinator
   ↓
Agent A
   ↓
wait
   ↓
Agent B
```

Instead:

```text
Coordinator
   ├──→ Agent A
   └──→ Agent B
```

Use **multiple Task tool calls in a single response**.

### Why?

**Lower latency.**

There's no reason for Agent B to wait for Agent A if their tasks are independent. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-3-subagent-invocation-context))

### Exam phrase to remember:

> **Independent tasks → parallel spawning.**

---

# 9. `fork_session` vs `resume` ⭐⭐

Another exam distinction.

### `resume`

Continue the **same session**.

```text
Session A
   ↓
continue
   ↓
Session A
```

### `fork_session`

Create an **independent branch** from the existing session.

```text
        Session A
           │
       ┌───┴───┐
       ↓       ↓
   Branch A  Branch B
```

The branches then work independently and don't see each other's results. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-3-subagent-invocation-context))

### Easy memory:

> **Resume = continue**  
> **Fork = branch**

---

# 10. Important Exam Traps 🚨

| Scenario | Correct answer |
|---|---|
| Coordinator cannot spawn subagents | Check `allowedTools` for `Task`/`Agent` |
| Subagent needs previous agent's results | Coordinator must explicitly pass them |
| Synthesis has missing citations | Check whether metadata was passed |
| Content passed without source information | Context-passing failure |
| Two independent research tasks | Spawn in parallel |
| Agent automatically knows coordinator history | ❌ No |
| Agent automatically knows another agent's output | ❌ No |
| Need to continue same session | `resume` |
| Need an independent branch | `fork_session` |
| Prompt should dictate every procedure | ❌ Prefer goal-oriented instructions |

---

# 🧠 One-Minute Revision

Remember this:

```text
TASK
 ↓
Coordinator needs Task/Agent in allowedTools
 ↓
Spawn subagent
 ↓
Subagent has ISOLATED context
 ↓
Coordinator explicitly passes context
 ↓
Pass CONTENT + METADATA
 ↓
Independent tasks? → PARALLEL
 ↓
Same session? → RESUME
New branch? → FORK
```

### The 5 things I'd memorise for the exam

**1. `allowedTools` → Task/Agent**  
**2. Subagents have isolated context**  
**3. Explicitly pass context**  
**4. Content + structured metadata**  
**5. Independent tasks → parallel Task calls**

These are the highest-value points from this section. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-3-subagent-invocation-context))

---

# 1.4 Workflow Enforcement & Handoff

Absolutely. Here’s **Domain 1 → Task 1.4: Workflow Enforcement & Handoff** simplified into **exam-focused notes**. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-4-workflow-enforcement-handoff))

# 1. The Big Idea ⭐⭐⭐

There are **two ways** to control an agent's workflow:

### Prompt-based guidance

Tell Claude what to do in the prompt:

> "Always verify the customer before processing a refund."

This is **probabilistic**.

Claude will usually follow it, but it can occasionally skip or reorder steps. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-4-workflow-enforcement-handoff))

### Programmatic enforcement

Use **code-level checks / prerequisite gates** to physically prevent an invalid action.

Example:

```text
get_customer
      ↓
verified?
      ↓ YES
process_refund
```

If verification hasn't happened:

```text
process_refund → BLOCKED ❌
```

This is **deterministic**. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-4-workflow-enforcement-handoff))

---

# 2. ⭐ Exam Decision Rule

Remember this:

> **Money + Security + Compliance → Programmatic enforcement**

| Situation | Correct approach |
|---|---|
| Refunds | Programmatic |
| Payments | Programmatic |
| Transfers | Programmatic |
| Identity verification | Programmatic |
| Access control | Programmatic |
| AML / regulatory checks | Programmatic |
| Formatting | Prompt is OK |
| Style preferences | Prompt is OK |
| Output ordering | Prompt is OK |

Why?

Because one failure in financial, security or compliance workflows can have serious consequences. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-4-workflow-enforcement-handoff))

---

# 3. Prerequisite Gate ⭐⭐⭐

A **prerequisite gate** is a programmatic check that prevents a tool from executing until a required condition is satisfied.

Example:

```text
get_customer()
      ↓
verified customer ID?
      ↓
     YES
      ↓
process_refund()
```

If not verified:

```text
process_refund()
      ↓
   BLOCKED ❌
```

Even if Claude tries to skip `get_customer`, the **code blocks it**. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-4-workflow-enforcement-handoff))

### Key distinction

**Prompt:**

> "Please verify before refunding."

Claude *should* follow it.

**Gate:**

> "Refund cannot execute unless verification exists."

Claude **cannot bypass it**.

---

# 4. The 8% Failure Scenario ⭐⭐⭐

This is a very important exam pattern.

Suppose:

```text
System prompt:
"Always verify identity before refund."
```

But production data shows:

```text
92% → correct
8%  → verification skipped
```

The correct solution is **NOT**:

❌ Stronger prompt  
❌ More instructions  
❌ Few-shot examples  
❌ Routing classifier

Correct:

✅ **Programmatic prerequisite gate**

```text
process_refund
      ↓
verified customer ID exists?
      ↓
   NO → BLOCK ❌
   YES → execute ✅
```

The goal is to make the workflow **deterministic**, rather than merely improving the probability that Claude follows instructions. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-4-workflow-enforcement-handoff))

---

# 5. Prompt vs Programmatic Enforcement

Think:

```text
PROMPT
  ↓
"Please do X before Y"
  ↓
Probabilistic
  ↓
Can occasionally fail
```

versus:

```text
CODE
  ↓
"Y cannot execute until X is complete"
  ↓
Deterministic
  ↓
Cannot bypass
```

### Exam shortcut 🧠

> **Prompt = guidance**  
> **Code = enforcement**

---

# 6. Exam Traps 🚨

### Trap 1: "Make the system prompt stronger"

If the problem involves **money/security/compliance**:

❌ Wrong.

A stronger prompt can reduce failures, but cannot guarantee zero failures. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-4-workflow-enforcement-handoff))

---

### Trap 2: Few-shot examples

> "Show Claude five examples of the correct workflow."

❌ Still probabilistic.

Few-shot examples improve behaviour but don't provide deterministic enforcement. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-4-workflow-enforcement-handoff))

---

### Trap 3: Routing classifier

Suppose refunds sometimes bypass verification.

Someone suggests:

> "Add a classifier that routes refund requests to the verification agent."

❌ Not the core solution.

The problem is **workflow enforcement inside the agent**, not which agent receives the request. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-4-workflow-enforcement-handoff))

---

# 7. Multi-Concern Requests ⭐⭐

A user may ask several things at once:

> "Return my order, update my address, and check my loyalty points."

Correct approach:

### Step 1 — Decompose

```text
Return order
Update address
Check loyalty points
```

### Step 2 — Investigate in parallel

```text
             Coordinator
            /     |      \
           ↓      ↓       ↓
       Return   Address  Loyalty
```

Use **shared relevant context** where appropriate.

### Step 3 — Synthesise

Produce **one unified resolution** addressing all concerns.

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-4-workflow-enforcement-handoff))

### Wrong ❌

Handle only the first request.

Or create completely separate sequential conversations unnecessarily.

---

# 8. Structured Handoff ⭐⭐⭐

When an agent cannot resolve something and must hand it to a human, the handoff must be **self-contained**.

Important exam fact:

> **The human agent does NOT have access to the conversation transcript.** ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-4-workflow-enforcement-handoff))

Therefore, the handoff needs these **5 fields**:

### Remember: **C-S-R-A-R**

1. **Customer ID**
2. **Conversation Summary**
3. **Root Cause Analysis**
4. **Refund Amount** — if applicable
5. **Recommended Action**

```text
Human Agent
    ↑
    |
Structured Handoff
    |
    ├── Customer ID
    ├── Summary
    ├── Root cause
    ├── Refund amount
    └── Recommended action
```

If important information is missing, the human may have to ask the customer to repeat everything. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-4-workflow-enforcement-handoff))

---

# 9. SubagentStart / SubagentStop

These are mentioned on the page as **background rather than tested material**. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-4-workflow-enforcement-handoff))

For exam priority, don't spend much time here.

If you do remember them:

### `SubagentStart`

Occurs when a subagent starts.

It is **observational** and **cannot block the invocation**.

To enforce rules before spawning, use a **PreToolUse hook on the Agent/Task tool**. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-4-workflow-enforcement-handoff))

### `SubagentStop`

Occurs when a subagent finishes.

It can validate the result.

**Exit code 2** can prevent completion and send the subagent back to work. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-4-workflow-enforcement-handoff))

---

# 🧠 One-Minute Revision

```text
WORKFLOW ENFORCEMENT
        ↓
Prompt = probabilistic
Code/Gate = deterministic
        ↓
Money / Security / Compliance
        ↓
PROGRAMMATIC ENFORCEMENT
```

### Prerequisite gate

```text
Required step completed?
        ↓
   NO → BLOCK
   YES → ALLOW
```

### Multi-concern request

```text
Decompose
   ↓
Parallel investigation
   ↓
Unified resolution
```

### Human handoff

```text
Customer ID
+ Summary
+ Root cause
+ Refund amount
+ Recommended action
```

## 🎯 Most important exam rules

**1. High-stakes workflow → programmatic enforcement.**

**2. Prompt instructions are not deterministic.**

**3. Few-shot examples are not deterministic.**

**4. Prerequisite gates physically block invalid tool calls.**

**5. Multi-concern requests → decompose + investigate in parallel + synthesise.**

**6. Human handoff → self-contained summary with all required fields.** ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-4-workflow-enforcement-handoff))

---

# 1.5 Agent SDK Hooks

Absolutely! Here are **simple, exam-focused notes** for **Domain 1 → Task 1.5: Agent SDK Hooks**. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-5-agent-sdk-hooks))

# 1. Big Idea ⭐⭐⭐

**Hooks = deterministic control around tool execution.**

They sit between Claude's decision and the real-world tool action.

```text
Claude
  ↓
Tool call
  ↓
┌─────────────────┐
│     Hook        │
└─────────────────┘
  ↓
Tool execution
```

The key distinction:

> **PreToolUse = before the tool runs**  
> **PostToolUse = after the tool runs**

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-5-agent-sdk-hooks))

---

# 2. Two Types of Hooks ⭐⭐⭐

| Hook | When? | Main purpose |
|---|---|---|
| **PreToolUse** | Before tool execution | Policy enforcement |
| **PostToolUse** | After tool execution | Data transformation / normalisation |

### Easy memory 🧠

> **PRE = Prevent**  
> **POST = Process the result**

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-5-agent-sdk-hooks))

---

# 3. PreToolUse Hook ⭐⭐⭐

A **PreToolUse** hook intercepts a tool call **before the tool executes**.

It can:

- Allow the call
- Deny/block the call
- Ask for approval
- Defer the decision
- Modify the tool input
- Redirect to another workflow

The tool **does not execute** if the hook blocks it. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-5-agent-sdk-hooks))

### Example

Suppose:

```text
process_refund(amount=$800)
```

Business rule:

> Refunds above $500 require human approval.

The flow becomes:

```text
Claude
  ↓
process_refund($800)
  ↓
PreToolUse
  ↓
Amount > $500?
  ↓
YES
  ↓
BLOCK ❌
  ↓
Human escalation
```

The refund tool **never runs**.

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-5-agent-sdk-hooks))

---

# 4. PreToolUse = Policy Enforcement

Use **PreToolUse** when something **must not happen unless a condition is satisfied**.

### Examples

#### Refund

```text
Refund > $500?
       ↓
     BLOCK
```

#### AML

```text
transfer_funds
       ↓
AML check completed?
       ↓
   NO → BLOCK
   YES → ALLOW
```

#### Manager approval

```text
Discount > 20%
       ↓
Manager approval?
       ↓
   NO → BLOCK
   YES → Execute
```

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-5-agent-sdk-hooks))

---

# 5. PostToolUse Hook ⭐⭐⭐

A **PostToolUse** hook runs **after the tool has executed**, but **before Claude processes the tool result**.

Its main purpose is to **transform or normalise the result**.

```text
Tool
 ↓
Executes
 ↓
PostToolUse
 ↓
Clean / normalise result
 ↓
Claude sees result
```

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-5-agent-sdk-hooks))

---

# 6. Data Normalisation ⭐⭐⭐

Different tools may return information in different formats.

For example:

### Tool A

```text
date: 1710489600
status: 200
```

### Tool B

```text
date: 2024-03-15T12:00:00Z
status: "active"
```

### Tool C

```text
date: 15/03/2024
status: "S"
```

This creates unnecessary complexity for Claude.

A **PostToolUse hook** can convert everything into one consistent format:

```text
All dates → ISO 8601
All status codes → human-readable strings
```

For example:

```text
2024-03-15T12:00:00Z
"shipped"
"pending"
"delivered"
```

Now Claude receives consistent data regardless of which tool produced it. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-5-agent-sdk-hooks))

---

# 7. 🚨 Biggest Exam Trap

Suppose the requirement is:

> "Block refunds above $500."

Someone proposes:

```text
PostToolUse hook
```

❌ **Wrong!**

Why?

Because:

```text
Tool executes
      ↓
Refund happens 💰
      ↓
PostToolUse
      ↓
Too late!
```

The side effect has already occurred.

### Correct:

```text
PreToolUse
     ↓
Check amount
     ↓
> $500?
     ↓
BLOCK
     ↓
Tool never executes
```

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-5-agent-sdk-hooks))

---

# 8. Another Important Trap

Suppose you need to normalise data:

> Convert Unix timestamps to ISO 8601.

Should you use **PreToolUse**?

❌ No.

The data doesn't exist until the tool executes.

Correct:

> **PostToolUse**

```text
Tool executes
      ↓
Tool result
      ↓
PostToolUse
      ↓
Normalise
      ↓
Claude
```

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-5-agent-sdk-hooks))

---

# 9. Hooks vs Prompts ⭐⭐⭐

This is directly connected to Task 1.4.

| Requirement | Use | Guarantee |
|---|---|---|
| Must happen 100% of the time | **Hook** | Deterministic |
| Preferred behaviour | **Prompt** | Probabilistic |

### Example: AML

Prompt:

> "Always perform AML verification before international transfers."

❌ Not enough if **100% compliance is required**.

Use:

```text
PreToolUse
     ↓
transfer_funds
     ↓
AML passed?
     ↓
NO → BLOCK
YES → ALLOW
```

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-5-agent-sdk-hooks))

---

# 10. When Prompts Are Enough

Not everything needs a hook.

For example:

> "Format responses using Markdown."

If Claude occasionally produces plain text, that's generally not a serious business failure.

A prompt is appropriate.

```text
Formatting preference
       ↓
Prompt
```

You don't need a deterministic hook for every preference. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-5-agent-sdk-hooks))

---

# 11. Decision Framework 🧠

Ask yourself:

### Question 1

> **Must this happen 100% of the time?**

If **YES**:

→ **Hook**

If **NO**:

→ Prompt may be sufficient.

### Question 2

If using a hook:

> **Do I need to prevent the action?**

→ **PreToolUse**

> **Do I need to transform the result?**

→ **PostToolUse**

---

# 12. Simple Decision Tree

```text
             What do you need?
                    │
          ┌─────────┴─────────┐
          ↓                   ↓
    Enforce policy       Transform result
          ↓                   ↓
    PreToolUse           PostToolUse
          ↓                   ↓
   Before execution       After execution
          ↓                   ↓
   Block / allow /       Normalise data
   modify / redirect
```

---

# 13. `permissionDecision` ⭐⭐

For **PreToolUse**, the hook can return a permission decision such as:

```text
allow
deny
ask
defer
```

It can also provide:

```text
updatedInput
```

to rewrite the tool's arguments before execution. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-5-agent-sdk-hooks))

### Remember

```text
PreToolUse
   ↓
permissionDecision
   ↓
allow / deny / ask / defer
```

---

# 14. `updatedToolOutput` ⭐⭐

A **PostToolUse** hook can replace what Claude sees using:

```text
updatedToolOutput
```

This allows the hook to transform the tool's output before the model processes it. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-5-agent-sdk-hooks))

### Example

```text
Tool output:
status = "P"

        ↓

PostToolUse

        ↓

Claude sees:
status = "pending"
```

---

# 15. High-Value Exam Scenarios 🎯

### Scenario A

> International transfers must always pass AML verification.

**Answer:**

✅ PreToolUse hook

Why?

Because the transfer must be **blocked before execution** if AML hasn't passed.

---

### Scenario B

> Three MCP tools return dates in different formats.

**Answer:**

✅ PostToolUse hook

Why?

Because the tools have already returned their data, and you need to **normalise the results before Claude processes them**.

---

### Scenario C

> Refunds over $500 require human approval.

**Answer:**

✅ PreToolUse hook

Why?

The refund must be prevented **before the tool executes**.

---

### Scenario D

> Claude should normally answer using Markdown.

**Answer:**

✅ Prompt

Why?

It's a **formatting preference**, not a critical business rule.

---

# 🚨 Exam Traps to Memorise

### Trap 1

**PostToolUse to block an action**

❌ Wrong — the action already happened.

### Trap 2

**Stronger prompt for 100% compliance**

❌ Wrong — prompts are probabilistic.

### Trap 3

**Use Claude to normalise all tool outputs**

❌ Not ideal — use PostToolUse for deterministic normalisation.

### Trap 4

**Confuse hook direction**

```text
Pre  → before execution
Post → after execution
```

---

# 🧠 One-Minute Revision

```text
             AGENT SDK HOOKS
                    │
          ┌─────────┴─────────┐
          ↓                   ↓
     PreToolUse          PostToolUse
          ↓                   ↓
      BEFORE                AFTER
          ↓                   ↓
   Policy enforcement    Data normalisation
          ↓                   ↓
    Block / Allow       Transform result
```

### The golden rule ⭐⭐⭐

> **Need to stop something? → PreToolUse.**  
> **Need to clean/transform the result? → PostToolUse.**  
> **Need 100% deterministic enforcement? → Hook.**  
> **Need a preference? → Prompt.**

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-5-agent-sdk-hooks))

---

# 1.6 Task Decomposition Strategies

Here are the **simple, exam-focused notes** from the page you shared. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-6-task-decomposition))

---

## 1. What is Task Decomposition?

**Task decomposition** means:

> Breaking a complex task into smaller pieces that an agentic system can handle.

For the exam, focus on **two patterns**:

1. **Fixed Sequential Pipeline**
2. **Dynamic Adaptive Decomposition**

And one important failure mode:

3. **Attention Dilution**

---

# 2. Fixed Sequential Pipeline ⭐⭐⭐

Also called **Prompt Chaining**.

The steps are:

```text
Step 1
  ↓
Step 2
  ↓
Step 3
  ↓
Step 4
```

The workflow is **defined in advance**.

The output from one step becomes the input for the next step. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-6-task-decomposition))

### Example

Code review:

```text
1. Analyse each file
        ↓
2. Cross-file integration analysis
        ↓
3. Generate final report
```

### Best for

Use a fixed pipeline when:

- Steps are known beforehand
- Input is structured
- The workflow is predictable

Examples:

- Code reviews
- Document processing
- Data extraction
- Compliance checks ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-6-task-decomposition))

### Advantages

✅ Consistent  
✅ Reliable  
✅ Easy to debug  
✅ Easy to monitor

### Limitation

❌ It **cannot adapt** if something unexpected is discovered.

---

# 3. Dynamic Adaptive Decomposition ⭐⭐⭐

Here the agent **creates and changes subtasks based on what it discovers**.

Instead of deciding everything beforehand:

```text
Goal
 ↓
Investigate
 ↓
Discover something
 ↓
Change plan
 ↓
Continue
```

The plan evolves as the agent learns more. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-6-task-decomposition))

### Example

You are investigating an unfamiliar legacy codebase.

```text
Map codebase
     ↓
Find important modules
     ↓
Create test plan
     ↓
Discover Module A depends on B
     ↓
B has no tests
     ↓
Change priority
     ↓
Test B first
```

The plan **adapts to new information**. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-6-task-decomposition))

### Best for

Use dynamic decomposition when:

- The scope is unknown
- You don't know the root cause
- New information can change the plan

Examples:

- Legacy codebase exploration
- Security audits
- Research
- Debugging unfamiliar systems ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-6-task-decomposition))

### Advantages

✅ Flexible  
✅ Adapts to discoveries  
✅ Good for open-ended problems

### Limitations

❌ Less predictable  
❌ Completion time is harder to estimate  
❌ More difficult to debug

---

# 4. Fixed vs Dynamic — Exam Shortcut 🧠

| Question | Fixed Pipeline | Dynamic Decomposition |
|---|---|---|
| Steps known beforehand? | ✅ | ❌ |
| Structured task? | ✅ | Usually ❌ |
| Open-ended task? | ❌ | ✅ |
| Adapts to discoveries? | ❌ | ✅ |
| Predictable? | ✅ | Less predictable |
| Code review with known process? | ✅ | ❌ |
| Unfamiliar system debugging? | ❌ | ✅ |

### Remember:

> **Known → Fixed**  
> **Unknown → Dynamic**

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-6-task-decomposition))

---

# 5. Attention Dilution ⭐⭐⭐

This is a **very important exam concept**.

### What is it?

Attention dilution happens when an agent tries to process **too many items in one pass**.

The result is:

> **Inconsistent depth of analysis.**

Some items get excellent analysis while others get shallow analysis. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-6-task-decomposition))

---

## 6. Typical Symptoms

Imagine an agent reviewing 14 files.

```text
Files 1–5
→ Detailed analysis ✅

Files 6–9
→ Moderate analysis ⚠️

Files 10–14
→ Superficial analysis ❌
```

You may see:

- Bugs missed in later files
- Detailed feedback early, shallow feedback later
- Same coding pattern flagged in one file but approved in another
- Inconsistent analysis quality ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-6-task-decomposition))

---

# 7. Why Does Attention Dilution Happen?

The model has to divide its attention across all the items in the context.

More items → less attention available per item.

So later items can receive less thorough analysis. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-6-task-decomposition))

---

# 8. The Correct Solution ⭐⭐⭐

The answer is:

> **Multi-pass architecture**

Not:

❌ Bigger context window  
❌ More powerful model  
❌ Stronger prompt

The architectural solution is:

```text
             14 Files
                │
      ┌─────────┼─────────┐
      ↓         ↓         ↓
   File 1     File 2    File 3 ... File 14
      ↓         ↓         ↓
   Local      Local      Local
   analysis   analysis   analysis
      └─────────┼─────────┘
                ↓
       Cross-file integration
                ↓
          Final findings
```

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-6-task-decomposition))

---

# 9. Two Layers of Multi-Pass Architecture ⭐⭐⭐

### Layer 1 — Per-item local analysis

Each file gets its **own analysis pass**.

```text
File 1 → Analysis
File 2 → Analysis
File 3 → Analysis
...
File 14 → Analysis
```

Each file receives dedicated attention. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-6-task-decomposition))

### Layer 2 — Cross-item integration

After all individual analyses:

```text
All individual results
        ↓
Cross-file analysis
        ↓
Find relationships
```

Look for:

- Data-flow problems
- API inconsistencies
- Cross-file dependencies
- Inconsistent patterns ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-6-task-decomposition))

---

# 10. Very Important: Batching Alone Is NOT Enough 🚨

Suppose you have 14 files.

Someone proposes:

```text
Batch 1 → Files 1–5
Batch 2 → Files 6–10
Batch 3 → Files 11–14
```

This reduces attention dilution **within each batch**.

But there is still a problem.

You might miss:

```text
File 2 → File 9 dependency
File 4 → File 12 API inconsistency
File 6 → File 14 data-flow problem
```

Therefore:

> **Batching without a cross-file integration pass is incomplete.** ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-6-task-decomposition))

---

# 11. The 14-File Exam Scenario 🎯

Suppose:

> An agent reviews 14 files in one pass.

Results:

```text
Files 1–5
→ Excellent

Files 6–9
→ Average

Files 10–14
→ Poor
```

It also says:

```text
File 3:
forEach is inefficient ❌

File 11:
same forEach pattern → approved ✅
```

### What is the root cause?

**Attention dilution.**

### Correct solution?

**Per-file analysis passes + cross-file integration pass.** ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-6-task-decomposition))

---

# 12. Exam Traps 🚨

### Trap 1 — "Use a bigger model"

> "The model is not powerful enough."

❌ Wrong.

Attention dilution is an **architectural problem**, not simply a model capability problem.

---

### Trap 2 — "Use a larger context window"

❌ Wrong.

A larger context window doesn't guarantee that every item receives equal attention.

---

### Trap 3 — "Write a stronger prompt"

> "Tell the model to review every file equally."

❌ Not enough.

A better prompt may improve average quality, but doesn't solve the underlying attention allocation problem.

---

### Trap 4 — Fixed pipeline for unknown investigation

Example:

> Debug an unfamiliar system where the root cause is unknown.

Using:

```text
Step 1 → Step 2 → Step 3 → Step 4
```

❌ Wrong choice.

Use:

> **Dynamic adaptive decomposition**

because discoveries may change the investigation plan. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-6-task-decomposition))

---

# 13. Quick Decision Tree 🧠

```text
Is the task predictable?
        │
   ┌────┴────┐
   YES       NO
    ↓         ↓
 Fixed      Dynamic
Pipeline   Adaptive
```

And:

```text
Too many items in one pass?
        ↓
Attention dilution
        ↓
Multi-pass architecture
        ↓
Per-item analysis
        ↓
Cross-item integration
```

---

# 14. One-Minute Revision Sheet

### Fixed Sequential Pipeline

> **Known steps → fixed order → predictable → reliable**

### Dynamic Adaptive Decomposition

> **Unknown scope → investigate → discover → adapt plan**

### Attention Dilution

> **Too many items in one pass → inconsistent depth**

### Solution

> **Per-item passes + cross-item integration**

### Critical exam distinction

```text
Fixed pipeline
= "I know the steps."

Dynamic decomposition
= "I don't know all the steps yet."

Multi-pass architecture
= "Give each item dedicated attention,
then analyse relationships between items."
```

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-6-task-decomposition))

### 🎯 Memorise these 5 points

1. **Known, structured task → Fixed Sequential Pipeline**
2. **Unknown, open-ended task → Dynamic Adaptive Decomposition**
3. **Too many items in one pass → Attention Dilution**
4. **Attention Dilution → Multi-pass architecture**
5. **Multi-pass = Per-item analysis + Cross-item integration**

---

# Domain 1 — Task 1.7: Session State & Resumption

Here are the **simple, exam-focused notes** for the page you shared. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-7-session-state-resumption))

---

## 1. What is Session Management?

Session management controls **how an agent continues, branches, or restarts work** across sessions.

This matters for long-running tasks such as:

- Debugging a large system
- Reviewing a codebase
- Multi-day research

The exam focuses on **three options**:

1. `--resume`
2. `fork_session`
3. Fresh session + summary injection ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-7-session-state-resumption))

---

# 2. `--resume` ⭐⭐⭐

### Meaning

`--resume` means:

> **Continue the existing session from where you stopped.**

It restores the conversation history, including:

- Tool results
- Previous analysis
- Reasoning
- Conversation context

```text
Session
   ↓
Stop
   ↓
--resume
   ↓
Continue same session
```

### Use `--resume` when:

✅ Previous context is still valid  
✅ Files have not changed significantly  
✅ You want to continue the same investigation

Example:

> You analysed a codebase yesterday and nothing has changed.

**Answer → `--resume`** ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-7-session-state-resumption))

---

# 3. Important: `--resume` Does NOT Create a Session

This is an easy exam detail.

`--resume` resumes a session that already exists.

You can name a session using:

```text
--name
```

and later resume it with:

```text
claude --resume <name>
```

There is also:

```text
--continue
```

which continues the most recent conversation in the current directory. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-7-session-state-resumption))

### Memory trick

> **Resume = continue an existing session.**

---

# 4. `fork_session` ⭐⭐⭐

### Meaning

`fork_session` creates a **new independent branch** from an existing session.

```text
             Original Session
                    │
             ┌──────┴──────┐
             ↓             ↓
         Branch A       Branch B
```

Both branches start with the **same baseline**, but after the fork they operate independently.

Changes/results in one branch do not affect the other. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-7-session-state-resumption))

---

## When to use `fork_session`

Use it when you want to explore **different approaches**.

Example:

> You analysed a codebase and want to compare two refactoring strategies.

```text
Original analysis
       ↓
     Fork
    /    \
   ↓      ↓
Refactor A  Refactor B
```

### Exam shortcut:

> **Fork = explore alternatives**

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-7-session-state-resumption))

---

# 5. `resume` vs `fork_session` ⭐⭐⭐

This distinction is extremely important.

| Requirement | Correct choice |
|---|---|
| Continue same investigation | `--resume` |
| Explore another approach | `fork_session` |
| Compare two strategies | `fork_session` |
| Continue yesterday's work | `--resume` |
| Create an independent branch | `fork_session` |

### Easy memory:

> **Resume = Continue**  
> **Fork = Diverge**

---

# 6. 🚨 The Stale Context Problem

This is the **central concept of Task 1.7**.

Imagine:

### Day 1

Claude reads:

```text
auth.ts
```

and sees:

```text
Old code
```

That file's contents are stored in the conversation history as a tool result.

### Day 2

You modify:

```text
auth.ts
```

Now the real file contains:

```text
New code
```

Then you use:

```text
--resume
```

### Problem

The old tool result is **still in the conversation history**.

So Claude may see:

```text
Conversation history
      ↓
Old auth.ts ❌

Current file
      ↓
New auth.ts ✅
```

This can cause **contradictory advice**. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-7-session-state-resumption))

---

# 7. Why `--resume` Can Be Wrong After File Changes 🚨

A common mistake is:

> "I'll resume the session and tell Claude to re-read the changed files."

This is **not the best solution**.

Why?

Because the old tool results are **still present** in the conversation history.

Claude can still reason from the outdated information. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-7-session-state-resumption))

### Exam answer:

❌ Resume + re-read changed files

Instead:

✅ **Fresh session + structured summary**

---

# 8. Fresh Session + Summary Injection ⭐⭐⭐

When previous tool results are stale:

```text
Old session
    ↓
Extract important findings
    ↓
Structured summary
    ↓
NEW session
    ↓
Inject summary
    ↓
Re-analyse changed files
```

The new session has:

✅ Previous important knowledge  
✅ Current file contents  
❌ No stale tool results

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-7-session-state-resumption))

---

# 9. What Goes Into the Summary?

The summary should preserve the **important findings**, not raw tool output.

For example:

```text
Previous findings:
- auth.ts had authentication issue
- session.ts had session validation issue
- middleware.ts had missing permission check

Modified files:
- auth.ts
- session.ts
- middleware.ts

Task:
Re-analyse these three files and verify the fixes.
```

Now Claude has the useful previous knowledge while working from the **current files**. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-7-session-state-resumption))

---

# 10. Targeted Re-Analysis ⭐⭐⭐

Suppose you have:

```text
50 files
```

but only:

```text
3 files changed
```

Don't analyse all 50 files again.

### Wrong ❌

```text
50 files → full re-analysis
```

Wasteful.

### Correct ✅

```text
Previous summary
      +
3 changed files
      ↓
Targeted re-analysis
```

The summary preserves knowledge about the other 47 unchanged files. ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-7-session-state-resumption))

### Exam memory:

> **Changed 3 → re-analyse 3, not 50.**

---

# 11. Three Session Options — Complete Picture 🧠

```text
                Session Management
                       │
        ┌──────────────┼──────────────┐
        ↓              ↓              ↓
     Resume          Fork         Fresh Start
        ↓              ↓              ↓
   Continue        Diverge       Stale context
   same work      alternatives    / degraded
                                  context
                                      ↓
                                Add summary
```

### `--resume`

**Continue**

Use when context is still valid.

### `fork_session`

**Branch**

Use when exploring different approaches.

### Fresh + summary

**Reset**

Use when old context contains stale information.

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-7-session-state-resumption))

---

# 12. Long Session with Too Much Clutter

A session can become very long.

The history may contain:

- Old tool results
- Irrelevant investigation
- Large amounts of intermediate information

This creates **degraded context**.

In this situation:

❌ Keep extending the same session

Better:

✅ Start fresh  
✅ Inject a structured summary  
✅ Continue from a clean baseline

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-7-session-state-resumption))

---

# 13. Dependency Updates 🚨

Suppose:

> You resume a session after updating dependencies.

Even if you didn't directly edit every affected file, dependency changes can affect multiple parts of the system.

Old tool results may therefore be stale.

### Better approach:

**Fresh session + summary + targeted re-analysis.** ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-7-session-state-resumption))

---

# 14. Exam Scenario 🎯

### Scenario

You have a **50-file codebase**.

Yesterday:

- Claude analysed all 50 files.
- It found issues in 3 files.

Today:

- You fixed those 3 files.
- You resume the old session.
- Claude recommends fixing the same issues again.

### What happened?

**Stale context.**

The old tool results still contain the previous versions of the files.

### Best solution?

✅ Start a **fresh session**  
✅ Inject a **structured summary**  
✅ Tell Claude which 3 files changed  
✅ Re-analyse only those 3 files

([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-7-session-state-resumption))

---

# 15. 🚨 Important Exam Traps

### Trap 1

> "Files changed, so use `--resume` and tell Claude to reread them."

❌ Wrong.

Old tool results remain in the session.

**Correct:** Fresh session + summary.

---

### Trap 2

> "Only 3 files changed, so analyse all 50 again."

❌ Wasteful.

**Correct:** Targeted re-analysis of the 3 changed files.

---

### Trap 3

> "Use `fork_session` because the old session has stale data."

❌ Wrong.

A fork inherits the existing history, including the stale tool results.

**Correct:** Fresh session + summary.

---

### Trap 4

> "Use `fork_session` to continue the same investigation."

❌ Wrong.

Fork is for **divergent exploration**.

Use `--resume` for continuation.

---

# 🧠 One-Minute Revision

### `--resume`

> **Same work + valid context**

```text
Continue
```

### `fork_session`

> **Different approaches + same starting point**

```text
Branch
```

### Fresh + summary

> **Stale/degraded context**

```text
Reset + preserve knowledge
```

### Stale context

> Old tool results no longer match the current files.

### Targeted re-analysis

> Re-analyse **only changed files**, not the entire codebase.

---

# 🎯 Memorise These 6 Rules

1. **`--resume` = continue the same session.**
2. **`fork_session` = create an independent branch.**
3. **Changed files + resume = possible stale context.**
4. **Stale context → fresh session + structured summary.**
5. **Don't re-analyse everything if only a few files changed.**
6. **Targeted re-analysis = changed files + preserved summary of unchanged work.** ([claudecertificationguide.com](https://claudecertificationguide.com/learn/1-agentic-architecture/1-7-session-state-resumption))
