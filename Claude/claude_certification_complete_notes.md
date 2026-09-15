# Claude Certified Architect — Complete Study Notes

> **Purpose:** Simple, structured notes for studying the Claude Certification Guide curriculum.
>
> **Study pattern:** Concept → Simple example → Exam rule → Common trap.

---

# 0. Curriculum Overview

| Domain | Topic | Weight |
|---|---|---:|
| 1 | Agentic Architecture & Orchestration | 27% |
| 2 | Tool Design & MCP Integration | 18% |
| 3 | Claude Code Configuration & Workflows | 20% |
| 4 | Prompt Engineering & Structured Output | 20% |
| 5 | Context Management & Reliability | 15% |
| **Total** | | **100%** |

---

# 1. Agentic Architecture & Orchestration — 27%

This is the largest domain. Focus on agent loops, orchestration, subagents, handoffs, hooks, decomposition, and sessions.

## 1.1 Agentic Loops

### Simple idea

An agentic loop means:

```text
User request
    ↓
Claude
    ↓
Check stop_reason
    ↓
tool_use? ── Yes ──→ Run tool
    ↑                    ↓
    └──── tool result ───┘

end_turn? → Finish
```

### Key concept

`stop_reason` tells the application what to do next.

- `tool_use` → continue the loop
- `end_turn` → stop

After a tool executes, its result must be added to the conversation before calling Claude again.

### Exam rule

> **`stop_reason` = authoritative loop-control signal.**

Iteration limits can be used as a safety net, but should not be the primary completion signal.

### Common trap

Do **not** determine completion by searching Claude's text for phrases such as "I'm finished."

---

## 1.2 Multi-Agent Orchestration

### Simple idea

Instead of one agent doing everything, use a coordinator and specialized subagents.

```text
                 Coordinator
                /     |      \
               ↓      ↓       ↓
          Research  Analysis  Writer
```

### Coordinator responsibilities

1. Receive the overall task
2. Break it into smaller tasks
3. Select subagents
4. Pass required context
5. Collect results
6. Validate results
7. Retry/refine when necessary
8. Produce the final result

### Key concept

> **Coordinator = central orchestrator of the multi-agent system.**

### Common pattern

**Hub-and-Spoke**

```text
             Coordinator
            /     |     \
           A      B      C
```

The coordinator communicates with and coordinates the specialized agents.

---

## 1.3 Subagent Invocation & Context Passing

### Simple idea

A subagent has its own context.

```text
Coordinator context
        ↓
   Explicitly pass
        ↓
Subagent context
```

A subagent does **not** automatically see everything in the coordinator's conversation.

### What to pass

A good delegation request can include:

```text
Task
Goal
Relevant context
Constraints
Expected output
```

### Example

```text
Task:
Analyze the authentication module.

Goal:
Find security problems.

Relevant context:
The application uses JWT authentication.

Expected output:
List vulnerabilities with file names and explanations.
```

### Key concept

> **Subagent = isolated context unless information is explicitly provided.**

### Exam terminology

The guide may use **Task** terminology, while current Claude Code terminology uses **Agent**. Know the relationship between the terms.

---

## 1.4 Workflow Enforcement & Handoff

### Problem

Suppose a workflow requires:

```text
Check account
    ↓
Verify payment
    ↓
Transfer money
```

A model might attempt to skip a prerequisite.

### Solution

Use programmatic enforcement.

```text
Check account
    ↓
Prerequisite satisfied?
    ↓ YES
Verify payment
    ↓
Transfer
```

### Key rule

If something **must always happen**, do not rely only on a prompt.

Use:

- Code
- Hooks
- Validation gates
- Programmatic state checks

### Handoff

When transferring work between agents, provide structured information:

```text
Task
Context
Current state
Required action
Expected output
Constraints
```

### Exam shortcut

> **"Must always / guaranteed / cannot bypass" → programmatic enforcement.**

---

## 1.5 Agent SDK Hooks

Hooks allow programmatic logic to execute around tool usage.

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

- Validation
- Security checks
- Blocking dangerous actions
- Checking prerequisites

### PostToolUse

Runs **after** tool execution.

Use it for:

- Output validation
- Auditing
- Normalization
- Result transformation

### Prompt vs Hook

| Prompt | Hook |
|---|---|
| Model instruction | Programmatic logic |
| Probabilistic | Deterministic |
| Good for preferences | Good for guarantees |
| Can be ignored/misinterpreted | Can enforce behavior |

### Exam shortcut

> **Guaranteed requirement → Hook/code.**

---

## 1.6 Task Decomposition Strategies

Large task:

```text
Build e-commerce application
        ↓
 ┌──────┼────────┐
 ↓      ↓        ↓
Auth   Payment   UI
```

### Sequential decomposition

Use when one step depends on another.

```text
A → B → C
```

Example:

```text
Research → Analyze → Write
```

### Parallel decomposition

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

These can be performed independently.

### Dynamic/adaptive decomposition

Use when the task structure is not known in advance.

```text
Task
 ↓
Claude decides how to split it
 ↓
Subtasks
```

### Exam shortcuts

- **Independent → Parallel**
- **Dependent → Sequential**
- **Unknown structure → Dynamic/adaptive**

---

## 1.7 Session State & Resumption

### Resume

Continue an existing session.

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

Useful for exploring another approach without changing the original path.

### Fresh start + summary

Start a new context and provide a structured summary.

Useful when the old context is:

- Too large
- Stale
- Contradictory
- No longer useful

### Exam shortcuts

- **Continue → Resume**
- **Branch → Fork**
- **Stale context → Fresh session + structured summary**

---

# 2. Tool Design & MCP Integration — 18%

This domain covers tool interfaces, errors, tool selection, MCP, and built-in tools.

---

## 2.1 Tool Interface Design

A good tool should have:

### Clear description

Explain:

- What it does
- Inputs
- Outputs
- Limitations
- Useful examples

### Good parameter names

Prefer:

```text
customer_email
```

over vague names such as:

```text
email
```

Use:

- `required`
- `enum`
- Sensible defaults
- Clear descriptions
- Appropriate types

### Key concept

> **Tool descriptions help Claude decide when and how to use a tool.**

### Common trap

A vague tool description can cause incorrect tool selection even if the underlying implementation is correct.

---

## 2.2 Structured Error Responses

Avoid:

```text
Something went wrong.
```

Prefer structured errors.

Example:

```json
{
  "errorCategory": "transient",
  "isRetryable": true,
  "description": "Service timeout"
}
```

### Important error categories

| Error | Meaning | Typical action |
|---|---|---|
| Transient | Temporary problem | Retry |
| Validation | Invalid input | Fix input |
| Business | Business-rule violation | Follow alternative path |
| Permission | Access denied | Escalate/fix authorization |

### Important distinction

Zero search results are not necessarily an error.

```text
Search → 0 results
       ↓
Valid result
```

But:

```text
API → 401
```

is an access/permission failure.

---

## 2.3 Tool Distribution & Tool Choice

### Problem

Giving one agent too many tools can make tool selection harder.

```text
Claude
  ↓
20+ tools
  ↓
Which one?
```

### Solutions

- Split tools across specialized agents
- Consolidate overlapping tools
- Improve tool descriptions
- Reduce unnecessary tool exposure

### `tool_choice`

Common modes:

```text
auto
any
specific tool
```

### `auto`

Claude decides whether to call a tool.

### `any`

Claude must call **some tool**, but chooses which.

### Specific/forced tool

Claude must call the specified tool.

### Exam shortcuts

- **Model decides → `auto`**
- **Must call a tool → `any`**
- **Must call this exact tool → forced/specific tool**

---

## 2.4 MCP Server Integration

MCP = **Model Context Protocol**

### Simple idea

MCP provides a standard way for AI applications to connect to external tools and data.

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

### MCP can expose

- Tools
- Resources
- Prompts

### Protocol

MCP uses **JSON-RPC 2.0** for protocol messages, with supported transports including **stdio** and **streamable HTTP**.

### Claude Code configuration

Project-level MCP configuration can be stored in:

```text
.mcp.json
```

Personal/global configuration can be stored in:

```text
~/.claude.json
```

### Exam shortcut

> **Need a standard external integration → think MCP.**

---

## 2.5 Built-in Tools

Know the purpose of common Claude Code tools:

| Tool | Purpose |
|---|---|
| `Grep` | Search content |
| `Glob` | Find files by pattern |
| `Read` | Read a known file |
| `Write` | Create/replace a file |
| `Edit` | Modify an existing file |
| `Bash` | Execute shell commands |

### Easy memory

```text
Glob → Where is the file?
Grep → Where is the text?
Read → Show me the file.
Edit → Change part of it.
Write → Create/replace it.
Bash → Run a command.
```

---

# 3. Claude Code Configuration & Workflows — 20%

---

## 3.1 CLAUDE.md Hierarchy, Scoping & Organization

`CLAUDE.md` contains instructions for Claude Code.

Think:

```text
User configuration
        ↓
Project configuration
        ↓
Directory-specific configuration
```

### Common locations

```text
~/.claude/CLAUDE.md
```

User-wide instructions.

```text
.claude/CLAUDE.md
```

Project-level instructions.

```text
CLAUDE.md
```

Directory-level instructions.

### Important

CLAUDE.md instructions are generally **combined**, not simply treated as one file overriding all others.

### Path-specific rules

Use:

```text
.claude/rules/
```

with path patterns.

Example:

```yaml
paths:
  - "src/api/**"
```

### Exam shortcut

> **Rules for specific paths → `.claude/rules/` + `paths`.**

---

## 3.2 Custom Slash Commands & Skills

### Commands

Commands are reusable prompt templates/workflows.

Examples:

```text
/review
/test
/deploy
```

Typical location:

```text
.claude/commands/
```

### Skills

Skills package reusable capabilities or workflows.

Example:

```text
.claude/skills/deploy/
    SKILL.md
```

### Easy distinction

```text
Command = reusable prompt/template

Skill = reusable capability/workflow
```

---

## 3.3 Path-Specific Rules

A rule may only apply to certain files.

Example:

```text
src/api/**
```

You might require:

- Authentication validation
- Structured API errors
- API-specific conventions

Instead of putting these rules globally, place them in:

```text
.claude/rules/
```

### Why?

It prevents irrelevant instructions from consuming context and affecting unrelated work.

### Exam shortcut

> **Path-specific requirement → path-scoped rule.**

---

## 3.4 Plan Mode vs Direct Execution

### Plan mode

Use when:

- Task is complex
- Multiple approaches exist
- Consequences are significant
- Requirements need clarification through analysis
- Several files/components are affected

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

- Task is simple
- Requirements are clear
- Solution is straightforward

Flow:

```text
Understand → Execute
```

### Exam shortcut

> **Complex/ambiguous → Plan mode.**  
> **Simple/clear → Direct execution.**

---

## 3.5 Iterative Refinement

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
Analyze failure
   ↓
Fix code
   ↓
Run tests again
```

### Good feedback

Bad:

```text
Improve the code.
```

Better:

```text
test_login_expired_token fails because
the function returns 200 instead of 401.
```

### Key concept

> **Concrete failure feedback produces better iterative correction.**

---

## 3.6 CI/CD Integration

CI/CD requires non-interactive execution.

Claude Code supports print/non-interactive mode using:

```bash
-p
```

or:

```bash
--print
```

### Conceptual pipeline

```text
Git push
   ↓
CI pipeline
   ↓
Claude Code -p
   ↓
Analyze code
   ↓
Machine-readable result
   ↓
Pipeline decision
```

Useful options include:

```text
--output-format json
--json-schema
```

### Exam shortcut

> **CI/CD → non-interactive / print mode.**

---

# 4. Prompt Engineering & Structured Output — 20%

---

## 4.1 System Prompts with Explicit Criteria

A strong system prompt should define:

### 1. Role

```text
You are a security reviewer.
```

### 2. Rules

```text
Identify authentication vulnerabilities.
```

### 3. Output requirements

```text
Return severity, file, line, and explanation.
```

### 4. Examples

Show expected behavior.

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

### Good practice

Concrete examples are often better than vague instructions.

Bad:

```text
Use good variable names.
```

Better:

```text
Bad:  x
Good: customer_count
```

---

## 4.2 Few-Shot Prompting

Few-shot prompting means giving Claude examples of the desired behavior.

Example:

```text
Input:
"Payment failed"

Output:
{
  "category": "payment_error"
}
```

Then provide a new input:

```text
Input:
"Card expired"
```

Claude follows the demonstrated pattern.

### Good examples

Include:

- Normal example
- Edge case
- Boundary case

### Exam shortcut

> **Few-shot = teach the desired pattern through examples.**

---

## 4.3 Structured Output with Tool Use

Suppose you need:

```json
{
  "name": "...",
  "email": "...",
  "age": 30
}
```

Simply saying:

```text
"Return valid JSON."
```

does not provide the strongest structural guarantee.

### Better approach

Use a schema/tool definition and appropriate tool choice.

```text
Claude
  ↓
Tool choice
  ↓
Defined schema
  ↓
Structured result
  ↓
Validation
```

### Important distinction

A schema can guarantee **structure**, but not necessarily **semantic correctness**.

Example:

```text
total = 100

items:
40 + 70 = 110
```

The output can be valid JSON while the calculation is wrong.

### Exam shortcut

> **Format guarantee → schema/tool.**  
> **Semantic correctness → validation.**

---

## 4.4 Validation, Retry & Feedback Loops

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
 Specific error feedback
```

### Don't say only:

```text
Try again.
```

Provide:

- Original request
- Previous output
- Specific validation error

Example:

```text
Error:
date must be ISO 8601.

Previous:
"next Tuesday"
```

Claude now has actionable feedback.

### Retry when

- Format is wrong
- Required field is missing
- Validation fails
- A transient tool error occurs

### Escalate when

- Repeated retries fail
- Fundamental misunderstanding remains
- Quality threshold is not met
- The system cannot safely decide

---

## 4.5 Batch Processing Strategies

Batch processing is appropriate for large amounts of work when immediate results are not required.

Example:

```text
1000 documents
      ↓
Batch processing
      ↓
Asynchronous results
```

### Good use cases

- Bulk classification
- Large document processing
- Offline analysis
- Non-interactive workloads

### Poor use cases

- Live chat
- Interactive UI
- Real-time responses

### Exam shortcut

> **Batch API = throughput/cost optimization for asynchronous work, not a real-time speed optimization.**

---

## 4.6 Multi-Instance & Multi-Pass Review

### Problem

If the same session generates and reviews its own work, it may be biased toward its original solution.

### Better

Use separate instances/passes.

```text
Instance A
   ↓
Generate code
   ↓
Instance B
   ↓
Review code
```

### Large codebase

Avoid one giant analysis:

```text
20 files
   ↓
One huge analysis
```

Prefer:

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

### Key concept

> **Separate passes reduce self-review bias and attention dilution.**

---

# 5. Context Management & Reliability — 15%

---

## 5.1 Context Window Management

### Problem

As context grows, important information can become harder to retain or retrieve.

```text
Context
████████████████████████
████████████████████████
████████████████████████
```

### Don't rely only on repeated summarization

Summaries can lose:

- Names
- Numbers
- Dates
- Exact requirements
- Important exceptions

### Better: persistent structured facts

Example:

```text
## Persistent Facts

Customer: ABC
Order: 12345
Status: Pending
Deadline: Sept 20
```

Keep important facts explicit.

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

A scratchpad provides external persistence.

### Exam shortcut

> **Important facts → store them explicitly.**

---

## 5.2 Escalation & Ambiguity Resolution

Escalate when:

1. The user explicitly requests a human
2. Policy does not cover the situation
3. The agent cannot make meaningful progress
4. The decision requires authority the agent does not have

### Policy violation vs policy gap

#### Policy violation

```text
Policy says NO
```

→ Follow policy / refuse the action.

#### Policy gap

```text
Policy says nothing
```

→ Escalate for human/business decision.

### Example

Policy:

```text
Refunds allowed within 30 days.
```

Customer:

```text
45-day refund
```

If the policy does not define what to do with the 45-day case:

→ **Escalate.**

---

## 5.3 Error Propagation in Multi-Agent Systems

Bad flow:

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
Bad final answer
```

Errors can propagate from one agent to another.

### Solution

Pass structured error information.

Include:

```text
Failure type
What was attempted
Partial results
Possible alternatives
```

### Example

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

### Key concept

> **Structured errors help downstream agents make better decisions.**

---

## 5.4 Codebase Exploration & Context Degradation

For large codebases:

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

Claude may:

- Repeat itself
- Contradict earlier information
- Ignore recent results
- Lose important details
- Make increasingly irrelevant changes

### Fix

Often use:

```text
Fresh context
     +
Structured summary
     +
Persistent findings
```

instead of continually adding more context.

---

## 5.5 Human Review & Confidence Calibration

Overall accuracy can hide weak categories.

Example:

```text
Overall accuracy = 95%

Category A = 99%
Category B = 98%
Category C = 60%
```

The overall number looks good, but Category C is problematic.

### Solution

Use stratified validation.

```text
All results
    ↓
Group by category
    ↓
Calculate metrics per category
    ↓
Find weak category
    ↓
Improve weak category
```

### Key concept

> **Don't trust aggregate accuracy alone.**

Look at performance by:

- Category
- Segment
- Difficulty
- Important edge cases

---

## 5.6 Information Provenance & Multi-Source Synthesis

When multiple sources provide information, preserve where each claim came from.

Instead of:

```text
"Claude has 1M context."
```

Prefer a structured record:

```text
Claim:
Claude has 1M context

Source:
Official documentation

Date:
2026-08-14
```

### Structured provenance

```json
{
  "claim": "...",
  "source": "...",
  "url": "...",
  "date": "..."
}
```

### Conflicting sources

Do not silently choose one.

Instead preserve:

```text
Source A → Claim X
Source B → Claim Y
Dates → ...
```

Then evaluate:

- Source authority
- Publication/update date
- Evidence
- Scope
- Possible reason for disagreement

### Exam shortcut

> **Multi-source synthesis → preserve provenance and conflicts.**

---

# 6. One-Page Memory Map

```text
CLAUDE CERTIFICATION
│
├── 1. AGENTS
│   ├── Agentic Loop
│   ├── Multi-Agent Orchestration
│   ├── Subagents
│   ├── Handoff
│   ├── Hooks
│   ├── Decomposition
│   └── Sessions
│
├── 2. TOOLS + MCP
│   ├── Tool Design
│   ├── Error Handling
│   ├── Tool Choice
│   ├── MCP
│   └── Built-in Tools
│
├── 3. CLAUDE CODE
│   ├── CLAUDE.md
│   ├── Commands / Skills
│   ├── Path Rules
│   ├── Plan Mode
│   ├── Refinement
│   └── CI/CD
│
├── 4. PROMPTS
│   ├── System Prompts
│   ├── Few-Shot
│   ├── Structured Output
│   ├── Validation / Retry
│   ├── Batch
│   └── Multi-Pass Review
│
└── 5. RELIABILITY
    ├── Context
    ├── Escalation
    ├── Error Propagation
    ├── Codebase Exploration
    ├── Human Review
    └── Provenance
```

---

# 7. High-Value Exam Rules

| Question clue | Think |
|---|---|
| `tool_use` | Continue loop |
| `end_turn` | Stop loop |
| Independent tasks | Parallel |
| Dependent tasks | Sequential |
| Unknown task structure | Dynamic/adaptive |
| Subagent needs information | Explicit context passing |
| Guaranteed enforcement | Hook / code |
| Before tool execution | `PreToolUse` |
| After tool execution | `PostToolUse` |
| Too many tools | Split/consolidate |
| Similar tools | Improve descriptions |
| Must call some tool | `tool_choice: any` |
| Must call exact tool | Forced/specific tool |
| MCP | Host → Client → Server |
| Temporary tool failure | Retry |
| Invalid input | Fix input |
| Zero search results | Valid empty result |
| Complex task | Plan mode |
| Path-specific instructions | `.claude/rules/` |
| Reusable prompt | Command |
| Reusable capability/workflow | Skill |
| CI/CD | Non-interactive / `-p` |
| Structured output | Schema/tool |
| Format validation failure | Retry with error feedback |
| Bulk/offline processing | Batch API |
| Self-review concern | Separate instance/pass |
| Long context | Persistent facts / scratchpad |
| Stale context | Fresh session + summary |
| Policy violation | Follow policy / refuse |
| Policy gap | Escalate |
| Weak category hidden by overall accuracy | Stratified validation |
| Conflicting sources | Preserve provenance |

---

# 8. Fast Revision Strategy

## Priority 1 — Memorize

```text
stop_reason
tool_use vs end_turn
parallel vs sequential
resume vs fork
PreToolUse vs PostToolUse
tool_choice: auto / any / specific
MCP architecture
CLAUDE.md / .claude/rules
Plan mode
-p / --print
structured output + schema
validation + retry
context degradation
escalation
provenance
```

## Priority 2 — Understand

For each concept, be able to answer:

1. What problem does it solve?
2. When should I use it?
3. When should I NOT use it?
4. What is the safer/more reliable alternative?
5. What is the common exam trap?

## Priority 3 — Practice

For every scenario, identify:

```text
Problem
  ↓
Constraint
  ↓
Best architecture/pattern
  ↓
Why?
  ↓
Why not the alternatives?
```

---

# 9. Final Mental Model

The whole certification can be remembered as one pipeline:

```text
USER
 ↓
AGENT
 ↓
PLAN / DECOMPOSE
 ↓
ORCHESTRATE
 ↓
SELECT TOOL
 ↓
MCP / EXTERNAL SYSTEM
 ↓
VALIDATE RESULT
 ↓
RETRY OR ESCALATE
 ↓
MANAGE CONTEXT
 ↓
FINAL OUTPUT
```

And the core reliability principle is:

> **Use the model for reasoning and flexible decisions; use schemas, validation, hooks, programmatic gates, and structured state for guarantees.**

---

# 10. Most Important "If You See This, Choose This" Cheatsheet

```text
IF task is independent
→ PARALLEL

IF task has dependencies
→ SEQUENTIAL

IF workflow step is mandatory
→ PROGRAMMATIC GATE / HOOK

IF subagent needs context
→ EXPLICITLY PASS CONTEXT

IF Claude should decide whether to use a tool
→ tool_choice = auto

IF Claude must use a tool but can choose which
→ tool_choice = any

IF Claude must use one exact tool
→ FORCE THAT TOOL

IF temporary tool failure
→ RETRY

IF invalid input
→ FIX INPUT

IF policy does not cover a case
→ ESCALATE

IF policy prohibits action
→ REFUSE / FOLLOW POLICY

IF context becomes stale
→ FRESH SESSION + STRUCTURED SUMMARY

IF important facts must survive context changes
→ PERSIST THEM EXPLICITLY

IF output format must be reliable
→ SCHEMA / STRUCTURED OUTPUT

IF structured output fails validation
→ RETRY WITH SPECIFIC ERROR

IF large asynchronous workload
→ BATCH

IF self-review bias is a concern
→ SEPARATE REVIEW INSTANCE

IF path-specific instructions
→ .claude/rules/

IF CI/CD automation
→ NON-INTERACTIVE / -p

IF sources conflict
→ PRESERVE PROVENANCE + SHOW CONFLICT
```

---

# 11. Final Summary

The five domains are:

1. **Agentic Architecture & Orchestration**
   - Build and control agent loops
   - Coordinate agents
   - Pass context explicitly
   - Enforce mandatory workflows
   - Decompose tasks correctly
   - Manage sessions

2. **Tool Design & MCP Integration**
   - Design clear tool interfaces
   - Return structured errors
   - Control tool selection
   - Understand MCP
   - Know built-in Claude Code tools

3. **Claude Code Configuration & Workflows**
   - Organize CLAUDE.md
   - Use commands and skills
   - Apply path-specific rules
   - Choose plan vs direct execution
   - Iterate using test feedback
   - Integrate with CI/CD

4. **Prompt Engineering & Structured Output**
   - Write explicit system prompts
   - Use few-shot examples
   - Use schemas for structure
   - Validate and retry
   - Batch asynchronous workloads
   - Use independent review passes

5. **Context Management & Reliability**
   - Protect important context
   - Escalate ambiguity safely
   - Prevent error propagation
   - Handle large codebases
   - Calibrate confidence
   - Preserve information provenance

## Core principle

> **Good agentic architecture is not just about making Claude more capable. It is about designing the surrounding system so that Claude's reasoning is guided, validated, constrained, recoverable, and observable.**
