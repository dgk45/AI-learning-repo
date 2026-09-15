# Claude Certified Architect — Complete Notes

## 🧭 Overall Structure

| Domain | Topic | Weight | Modules |
|---|---|---:|---:|
| 1 | Agentic Architecture & Orchestration | **27%** | 7 |
| 2 | Tool Design & MCP Integration | **18%** | 5 |
| 3 | Claude Code Configuration & Workflows | **20%** | 6 |
| 4 | Prompt Engineering & Structured Output | **20%** | 6 |
| 5 | Context Management & Reliability | **15%** | 6 |
| | **Total** | **100%** | **30** |

---

# 1️⃣ Agentic Architecture & Orchestration — 27%

This is the **largest domain**, so give it high priority. It covers how Claude agents execute tasks, use tools, coordinate subagents, enforce workflows, and maintain state.

---

## 1.1 Agentic Loops

### Simple idea

An **agentic loop** means:

> Claude thinks → uses tool → gets result → thinks again → continues → finishes.

### Basic flow

```text
User request
     ↓
Claude
     ↓
Check stop_reason
     ↓
 ┌───────────────┐
 │               │
tool_use       end_turn
 │               │
 ↓               ↓
Run tool       Finish
 │
 ↓
Add tool result
to conversation
 │
 └────→ Claude again
```

### Important

`stop_reason` tells your program what to do next.

- `tool_use` → **continue**
- `end_turn` → **stop**

Tool result must be added to the conversation before the next Claude call.

### ❌ Don't do this

Don't determine completion by looking for text like:

> "I'm finished."

Don't use an iteration counter as the primary stopping mechanism.

### Exam note

**`stop_reason` = authoritative loop-control signal.**

Iteration limits are only a **safety net** against runaway agents.

---

# 1.2 Multi-Agent Orchestration

### Simple idea

Instead of one agent doing everything:

```text
             Coordinator
            /     |      \
           ↓      ↓       ↓
       Research  Analysis  Writer
```

This is called **Hub-and-Spoke**.

### Coordinator

The coordinator:

1. Receives task
2. Breaks task into smaller tasks
3. Chooses subagents
4. Sends context
5. Collects results
6. Checks results
7. Refines/retries if necessary
8. Produces final answer

### Subagents

Each subagent performs a **specialised job**.

Example:

```text
Coordinator
   ↓
Research Agent
   ↓
Analysis Agent
   ↓
Writing Agent
```

For the exam, assume communication between subagents goes through the coordinator.

### Key concept

**Coordinator = brain of the multi-agent system.**

---

# 1.3 Subagent Invocation & Context Passing

### Simple idea

A subagent has its **own context**.

It does NOT automatically see the coordinator's conversation.

```text
Coordinator Context
       ❌
       ↓
Subagent Context
```

Therefore:

> **Context must be explicitly passed.**

### Example

Bad:

```text
"Analyze this."
```

Better:

```text
Task:
Analyze the authentication module.

Goal:
Find security problems.

Relevant context:
The application uses JWT authentication.

Expected output:
List vulnerabilities with file names.
```

### Important

The coordinator needs the appropriate delegation mechanism:

- Exam terminology: **Task tool**
- Current Claude Code terminology: **Agent**

The guide notes that `Agent` is the current name, while `Task` remains relevant to the exam terminology.

### Remember

**Subagent = isolated context.**

---

# 1.4 Workflow Enforcement & Handoff

### Problem

Suppose you have:

```text
Check account
   ↓
Verify payment
   ↓
Transfer money
```

Claude might try to jump directly to:

```text
Transfer money
```

That is dangerous.

### Solution

Use **programmatic enforcement**.

```text
Check account
     ↓
Prerequisite satisfied?
     ↓ YES
Verify payment
     ↓
Transfer
```

### Key concept

If something **must always happen**, don't rely only on a prompt.

Use code/hook/gate enforcement.

### Handoff

When passing work between agents, provide structured information:

```text
Task
Context
Current state
Required action
Expected output
Constraints
```

### Exam rule

**"Must always / guaranteed / cannot bypass" → programmatic enforcement.**

---

# 1.5 Agent SDK Hooks

Hooks allow code to run around tool execution.

### Two important hooks

```text
        Claude
          ↓
    PreToolUse
          ↓
       Tool
          ↓
    PostToolUse
          ↓
       Result
```

### PreToolUse

Runs **before** tool execution.

Use it for:

- validation
- security checks
- blocking dangerous actions
- checking prerequisites

### PostToolUse

Runs **after** tool execution.

Use it for:

- output validation
- normalization
- auditing
- transforming results

### Important distinction

| Prompt | Hook |
|---|---|
| Probabilistic | Deterministic |
| Model instruction | Programmatic |
| Can be ignored | Enforced by code |
| Good for preferences | Good for guarantees |

### Exam shortcut

> **Guaranteed requirement → Hook**

---

# 1.6 Task Decomposition Strategies

### Simple idea

Large task → break into smaller tasks.

Example:

```text
Build e-commerce application
        ↓
 ┌──────┼────────┐
 ↓      ↓        ↓
Auth   Payment   UI
```

### Two important strategies

#### Sequential

Use when one step depends on another.

```text
A → B → C
```

Example:

```text
Research → Analyze → Write
```

#### Parallel

Use when tasks are independent.

```text
       ┌→ A
Task → ├→ B
       └→ C
```

Example:

```text
Research:
- Solar
- Wind
- Geothermal
```

These can happen independently.

### Dynamic Adaptive

Use when you don't know the decomposition beforehand.

```text
Task
 ↓
Claude decides
how to split it
 ↓
Subtasks
```

### Exam shortcut

- **Independent → Parallel**
- **Dependent → Sequential**
- **Unknown complexity → Dynamic adaptive**

---

# 1.7 Session State & Resumption

There are three important ideas:

### Resume

Continue the existing session.

```text
Session A
   ↓
Continue Session A
```

### Fork

Create a branch from an existing session.

```text
             Session A
             /       \
            ↓         ↓
       Original     Branch
```

Useful when you want to explore another approach without changing the original path.

### Fresh start + summary

Start a new context and provide a carefully prepared summary.

Useful when old context has become:

- stale
- contradictory
- too large

### Exam shortcut

**Continue → Resume**

**Branch → Fork**

**Stale context → Fresh session + structured summary**

---

# 2️⃣ Tool Design & MCP Integration — 18%

This domain covers how Claude interacts with tools and external systems.

---

# 2.1 Tool Interface Design

A good tool needs:

### Description

Explain:

- what it does
- input
- output
- limitations
- examples

### Example

Bad:

```text
search()
```

Better:

```text
Search customer orders by customer_email
and optional order_date_range.
Returns matching orders.
```

### Schema

Use descriptive parameters:

```text
customer_email
```

instead of:

```text
email
```

Use:

- `required`
- `enum`
- sensible defaults
- clear descriptions

### Key concept

Claude primarily uses the **tool description** to decide which tool to use.

---

# 2.2 Structured Error Responses

Don't return:

```text
Something went wrong.
```

Instead return structured information.

Example:

```json
{
  "errorCategory": "transient",
  "isRetryable": true,
  "description": "Service timeout"
}
```

### Four important error categories

| Error | Meaning | Retry? |
|---|---|---|
| Transient | Temporary problem | ✅ Yes |
| Validation | Bad input | ❌ Fix input |
| Business | Business rule violation | ❌ Alternative path |
| Permission | Access denied | ❌ Escalate |

### Very important

A search returning zero results is **not necessarily an error**.

```text
Search → 0 results
       ↓
Valid result
```

But:

```text
API → 401
```

is an access failure.

---

# 2.3 Tool Distribution & Tool Choice

### Problem

If an agent has 20+ tools:

```text
Claude
 ↓
20 tools
 ↓
Which one?
😵
```

Tool selection becomes harder.

### Solution

Use specialised agents.

```text
Research Agent
 → 4 tools

Database Agent
 → 4 tools

Deployment Agent
 → 3 tools
```

### If two tools are similar

Improve their descriptions.

### If many tools have the same job

Consider consolidating them into one parameterised tool.

### `tool_choice`

Three important modes:

```text
auto
any
specific tool
```

### `auto`

Claude decides whether to call a tool.

### `any`

Claude must call **some tool**, but chooses which.

### Specific tool

Claude must call that particular tool.

### Exam shortcut

**Model decides → `auto`**

**Must call a tool → `any`**

**Must call this exact tool → forced tool**

---

# 2.4 MCP Server Integration

MCP = **Model Context Protocol**

Think:

> MCP is a standard way for AI applications to connect to external tools/data.

### Architecture

```text
Host
 ↓
MCP Client
 ↓
MCP Server
 ↓
External System
```

Example:

```text
Claude Desktop
      ↓
    Client
      ↓
Database MCP Server
      ↓
PostgreSQL
```

### MCP server can expose

- Tools
- Resources
- Prompts

### Protocol

MCP communication uses **JSON-RPC 2.0**, with transports such as **stdio** or **streamable HTTP**.

### Configuration

Project:

```text
.mcp.json
```

Personal/global:

```text
~/.claude.json
```

### Exam shortcut

**Need common integration? → Check community MCP server first.**

---

# 2.5 Built-in Tools

Know what common Claude Code tools are for:

| Tool | Purpose |
|---|---|
| `Grep` | Search content |
| `Glob` | Find files |
| `Read` | Read known file |
| `Write` | Create/replace file |
| `Edit` | Modify existing file |
| `Bash` | Execute shell commands |

### Easy memory

```text
Glob → Where is the file?
Grep → Where is the text?
Read → Show me the file.
Edit → Change part of it.
Write → Create/replace it.
Bash → Run command.
```

---

# 3️⃣ Claude Code Configuration & Workflows — 20%

This domain focuses on configuring Claude Code and building development workflows.

---

# 3.1 CLAUDE.md Hierarchy, Scoping & Organisation

`CLAUDE.md` contains instructions for Claude Code.

Think:

```text
User configuration
       ↓
Project configuration
       ↓
Directory configuration
```

Important:

> CLAUDE.md files are **combined/concatenated**, not simply overridden.

### Common locations

```text
~/.claude/CLAUDE.md
```

User-wide.

```text
.claude/CLAUDE.md
```

Project-wide.

```text
CLAUDE.md
```

Directory-specific.

### Important

If you have path-specific rules, use:

```text
.claude/rules/
```

with `paths` patterns.

Example:

```yaml
paths:
  - "src/api/**"
```

---

# 3.2 Custom Slash Commands & Skills

### Commands

Example:

```text
/review
/deploy
/test
```

Commands are reusable prompt templates.

Usually:

```text
.claude/commands/
```

### Skills

Skills are reusable capabilities/workflows.

Structure:

```text
.claude/skills/deploy/
    SKILL.md
```

### Easy difference

```text
Command = reusable prompt/template

Skill = reusable capability/workflow
```

---

# 3.3 Path-Specific Rules

Sometimes a rule should apply only to certain files.

Example:

```text
src/api/**
```

You might require:

```text
Use authentication validation.
Use structured API errors.
```

Instead of putting these rules into the global CLAUDE.md, use:

```text
.claude/rules/
```

### Why?

Because irrelevant rules consume context.

So:

> **Path-specific requirement → `.claude/rules/` + `paths`**

---

# 3.4 Plan Mode vs Direct Execution

### Plan Mode

Use when:

- task is complex
- multiple approaches exist
- consequences are significant
- you need to think before modifying files

Flow:

```text
Understand
   ↓
Plan
   ↓
Execute
```

### Direct execution

Use when:

- task is simple
- requirements are clear
- solution is obvious

```text
Understand → Execute
```

### Exam shortcut

**Complex/ambiguous → Plan mode**

**Simple/clear → Direct execution**

---

# 3.5 Iterative Refinement

Don't expect the first output to be perfect.

Use:

```text
Generate
   ↓
Test
   ↓
Find problem
   ↓
Fix
   ↓
Test again
```

For coding:

```text
Write code
 ↓
Run tests
 ↓
Test fails
 ↓
Claude sees failure
 ↓
Fix code
 ↓
Run tests again
```

### Important

Use concrete feedback.

Bad:

> Improve the code.

Better:

> Test `test_login_expired_token` fails because the function returns 200 instead of 401.

---

# 3.6 CI/CD Integration

CI/CD requires automation.

Claude Code can run in **non-interactive mode**.

Important flag:

```bash
-p
```

or:

```bash
--print
```

### Example concept

```text
Git push
   ↓
CI pipeline
   ↓
Claude Code -p
   ↓
Analyze code
   ↓
JSON result
   ↓
Pipeline decision
```

### Useful options

```text
--output-format json
--json-schema
```

for machine-readable output.

### Exam shortcut

**CI/CD → `-p` / non-interactive mode**

---

# 4️⃣ Prompt Engineering & Structured Output — 20%

This is another high-weight domain.

---

# 4.1 System Prompts with Explicit Criteria

A good system prompt should define:

### 1. Role

```text
You are a security reviewer.
```

### 2. Rules

```text
Identify authentication vulnerabilities.
```

### 3. Output

```text
Return severity, file, line and explanation.
```

### 4. Examples

Show expected behaviour.

### Simple structure

```text
ROLE
 ↓
RULES
 ↓
OUTPUT FORMAT
 ↓
EXAMPLES
```

### Important

Concrete examples are often better than vague instructions.

Instead of:

> Use good variable names.

Show:

```text
Bad: x
Good: customer_count
```

---

# 4.2 Few-Shot Prompting

Few-shot means:

> Give Claude examples of what you want.

### Example

```text
Input:
"Payment failed"

Output:
{
  "category": "payment_error"
}
```

Then:

```text
Input:
"Card expired"

Claude learns the pattern.
```

### Best practice

Usually:

> **2–4 strong examples**

rather than 10+ examples.

Include:

- normal example
- edge case
- boundary case

---

# 4.3 Structured Output with Tool Use

Suppose you want:

```json
{
  "name": "...",
  "email": "...",
  "age": 30
}
```

Don't depend only on:

> "Please return JSON."

That doesn't guarantee valid structure.

### Better

Define a tool/schema and force the tool call.

```text
Claude
 ↓
tool_choice
 ↓
Structured schema
 ↓
Validated structure
```

### Important distinction

Schema helps guarantee **format**.

It does NOT guarantee that the information is **semantically correct**.

Example:

```text
total = 100

items:
40 + 70 = 110
```

JSON can be perfectly valid while the data is wrong.

---

# 4.4 Validation, Retry & Feedback Loops

Basic pattern:

```text
Generate
   ↓
Validate
   ↓
Valid?
 ┌───┴───┐
Yes     No
 ↓       ↓
Done   Retry
         ↓
      Error feedback
```

### Don't say only:

> Try again.

Instead provide:

```text
Original request
+
Previous output
+
Specific validation error
```

Example:

```text
Error:
date must be ISO 8601.

Previous:
"next Tuesday"
```

Claude can correct it.

### Retry when

- format is wrong
- field is missing
- validation error

### Escalate when

- repeated failures
- fundamental misunderstanding
- confidence/quality threshold is not met

---

# 4.5 Batch Processing Strategies

Batch API is for:

> **Large amounts of work where immediate results aren't required.**

Think:

```text
1000 documents
      ↓
Batch
      ↓
Process asynchronously
```

### Good for

- bulk classification
- large document processing
- offline analysis

### Not good for

- live chat
- interactive UI
- real-time responses

The guide emphasizes Batch API as a **cost/throughput optimization, not a speed optimization**.

---

# 4.6 Multi-Instance & Multi-Pass Review

### Problem

Claude writes code.

Then the **same session** reviews its own code.

It may have bias toward its original solution.

### Better

```text
Instance A
   ↓
Generate code

Instance B
   ↓
Review code
```

Instance B doesn't have the original reasoning.

### Large codebase

Don't do:

```text
20 files
 ↓
One huge analysis
```

Instead:

```text
File 1 → Analysis
File 2 → Analysis
File 3 → Analysis
...
      ↓
Cross-file integration
      ↓
Final review
```

This reduces **attention dilution**.

---

# 5️⃣ Context Management & Reliability — 15%

This domain is about keeping agents reliable when tasks become large or complicated.

---

# 5.1 Context Window Management

### Problem

Long conversation:

```text
Context
████████████████████████
████████████████████████
████████████████████████
```

Important information may get lost.

### Don't rely heavily on repeated summarization

Every summary can lose:

- names
- numbers
- dates
- exact details

### Better

Use persistent structured facts.

```text
## Persistent Facts

Customer: ABC
Order: 12345
Status: Pending
Deadline: Sept 20
```

Keep important facts **verbatim**.

### Scratchpad

For long tasks:

```text
Claude
 ↓
Findings
 ↓
scratchpad.md
 ↓
Context reset
 ↓
Read scratchpad
 ↓
Continue
```

Scratchpad = external persistence.

---

# 5.2 Escalation & Ambiguity Resolution

When should an agent escalate to a human?

### Three major triggers

1. User explicitly asks for human
2. Policy doesn't cover the situation
3. Agent cannot make meaningful progress

### Example

Policy says:

```text
Refunds allowed within 30 days.
```

Customer asks about:

```text
45-day refund
```

Policy is silent?

→ **Escalate**

### Important distinction

**Policy violation**

```text
Policy says NO
```

→ Refuse according to policy.

**Policy gap**

```text
Policy doesn't say
```

→ Escalate.

---

# 5.3 Error Propagation in Multi-Agent Systems

Suppose:

```text
Agent A
 ↓
Bad result
 ↓
Agent B
 ↓
Bad result
 ↓
Agent C
 ↓
Very bad final answer
```

Errors propagate.

### Solution

Pass structured error information.

Include:

```text
Failure type
What was attempted
Partial results
Possible alternatives
```

### Bad

```text
Something failed.
```

### Good

```text
Failure:
network_timeout

Attempted:
Database query X

Partial:
Customer data retrieved
Payment data unavailable

Alternative:
Try backup database
```

---

# 5.4 Codebase Exploration & Context Degradation

When exploring a large codebase:

### Don't do everything at once.

Use:

```text
Discover
 ↓
Identify relevant files
 ↓
Analyze files
 ↓
Store findings
 ↓
Integrate findings
```

### Signs of context degradation

Claude starts:

- repeating itself
- contradicting earlier information
- ignoring recent results
- losing important details

### Fix

Often:

> **Fresh context + structured summary**

rather than simply adding more context.

---

# 5.5 Human Review & Confidence Calibration

Suppose your system reports:

```text
95% accuracy
```

Sounds great.

But maybe:

```text
Category A → 99%
Category B → 98%
Category C → 60%
```

Overall accuracy hides the problem.

### Solution

Use **stratified validation**.

```text
All results
    ↓
Group by category
    ↓
Calculate accuracy/category
    ↓
Find weak category
    ↓
Improve that category
```

### Key idea

> Don't trust only aggregate accuracy.

---

# 5.6 Information Provenance & Multi-Source Synthesis

When multiple sources provide information, preserve the source.

Instead of:

```text
"Claude has 1M context."
```

Store:

```text
Claim:
Claude has 1M context

Source:
Anthropic documentation

Date:
2026-08-14
```

### Why?

During synthesis, plain citations can disappear.

Structured provenance survives:

```text
{
  claim: "...",
  source: "...",
  url: "...",
  date: "..."
}
```

### Conflicting sources

Don't silently choose one.

Instead:

```text
Source A → claim X
Source B → claim Y
Dates → ...
```

Then allow the consumer to evaluate the difference.

---

# 🧠 One-Page Memory Map

If you want to remember the **entire certification**, use this:

```text
CLAUDE CERTIFICATION
│
├── 1. AGENTS
│   ├── Loop
│   ├── Multi-agent
│   ├── Subagents
│   ├── Handoff
│   ├── Hooks
│   ├── Decomposition
│   └── Sessions
│
├── 2. TOOLS + MCP
│   ├── Tool design
│   ├── Errors
│   ├── Tool choice
│   ├── MCP
│   └── Built-in tools
│
├── 3. CLAUDE CODE
│   ├── CLAUDE.md
│   ├── Commands / Skills
│   ├── Path rules
│   ├── Plan mode
│   ├── Refinement
│   └── CI/CD
│
├── 4. PROMPTS
│   ├── System prompts
│   ├── Few-shot
│   ├── Structured output
│   ├── Validation/retry
│   ├── Batch
│   └── Multi-pass review
│
└── 5. RELIABILITY
    ├── Context
    ├── Escalation
    ├── Error propagation
    ├── Codebase exploration
    ├── Human review
    └── Provenance
```

---

# 🔥 Most Important Exam Rules

These are the rules I would memorize first.

| If the question says... | Think... |
|---|---|
| `tool_use` | **Continue loop** |
| `end_turn` | **Stop loop** |
| Independent tasks | **Parallel** |
| Dependent tasks | **Sequential** |
| Unknown task structure | **Dynamic adaptive** |
| Subagent needs information | **Explicit context passing** |
| Guaranteed enforcement | **Hook / code** |
| Before tool execution | **PreToolUse** |
| After tool execution | **PostToolUse** |
| Too many tools | **Split/consolidate** |
| Similar tools, small set | **Improve descriptions** |
| Must call some tool | **`tool_choice: any`** |
| Must call specific tool | **Forced tool choice** |
| MCP | **Host → Client → Server** |
| Temporary tool failure | **Retry** |
| Invalid input | **Fix input** |
| No search results | **Valid empty result** |
| Complex task | **Plan mode** |
| CI/CD | **`-p`** |
| Path-specific instructions | **`.claude/rules/`** |
| Reusable workflow | **Skill** |
| Reusable prompt | **Command** |
| Guaranteed JSON structure | **Tool/schema** |
| Missing optional data | **Nullable/optional field** |
| Validation failure | **Retry with error feedback** |
| Bulk/offline processing | **Batch API** |
| Self-review | **Separate instance** |
| Long context | **Persistent facts / scratchpad** |
| Context becoming stale | **Fresh start + summary** |
| Policy gap | **Escalate** |
| Policy violation | **Refuse** |
| High overall accuracy but complaints | **Stratified validation** |
| Conflicting sources | **Preserve provenance** |

---

# 📚 Suggested Study Order

For your preparation, I'd go:

**1 → Agentic Loops → 1.2–1.7 → 2 → 3 → 4 → 5**

And within each topic use:

**Concept → Simple example → Exam rule → Common trap → Practice question.**

The site's current curriculum contains exactly these **30 task statements**, and its build-exercise section maps one practical exercise to each lesson.
