# Domain 2 — Task 2.1: Tool Interface Design

Here are **simple, exam-focused notes** for the page you shared…

---

## 1. What You Need to Know

Tool descriptions are the **PRIMARY mechanism LLMs use for tool selection**, not supplementary metadata.

A production-grade tool description has **5 elements**:

1. What the tool does / purpose
2. Inputs: data types, formats, constraints, required/optional
3. Example queries
4. Edge cases and limitations, including what it does NOT do
5. Explicit boundaries: when to use THIS tool vs similar tools

### Memory

**Purpose → Inputs → Examples → Edge cases → Boundaries**

**P-I-E-E-B**

---

## 2. Minimal vs Production-Grade Description

Minimal descriptions such as:

```text
get_customer: "Retrieves customer information"
lookup_order: "Retrieves order details"
```

can cause misrouting.

Production-grade descriptions should include:

- Identifiers
- Return values
- When to use
- When NOT to use

---

## 3. Misrouting Problem

Example:

> “check my order #12345”

Claude may incorrectly route the request to:

```text
get_customer
```

instead of:

```text
lookup_order
```

The root problem is that the tool descriptions are too similar or too vague.

---

## 4. Correct First Fix

The first fix should be:

> **Expand the tool descriptions.**

Do NOT start with:

- Few-shot examples
- A routing classifier
- Tool consolidation

### Why?

Better descriptions directly improve the model's understanding of tool purpose and boundaries.

---

## 5. Why NOT Few-Shot?

Few-shot examples can help, but they are not the best first fix for basic tool misrouting.

They:

- Add token overhead
- Do not directly fix unclear tool definitions
- Can become difficult to maintain

### Exam rule

> **Weak descriptions → improve descriptions first.**

---

## 6. Why NOT Routing Classifier?

A routing classifier can be useful in larger systems, but it is over-engineered as the first response to simple tool ambiguity.

It:

- Adds infrastructure
- Adds complexity
- Bypasses some of the model's natural language understanding

### Exam rule

> **Fix the tool descriptions before adding a routing classifier.**

---

## 7. Why NOT Consolidate?

Tool consolidation can be valid long-term, but it costs more and may change the interface.

If you only have a few tools and their purposes are hard to distinguish:

> **Improve the descriptions first.**

---

## 8. Tool Splitting

A broad generic tool such as:

```text
analyze_document
```

can be split into narrow tools:

```text
extract_data_points
summarize_content
verify_claim_against_source
```

Each tool should have **one clear job**.

### Benefit

Claude can make a more precise tool-selection decision because each tool has a clearly defined purpose.

---

## 9. Tool Splitting — Easy Memory

```text
analyze_document
       ↓
 ┌─────┼────────────────────┐
 ↓     ↓                    ↓
extract summarize       verify
data    content          claim
```

### Memory

> **One tool = one clear job.**

---

## 10. Tool Renaming

If a tool has a confusing name such as:

```text
analyze_content
```

it can be renamed to something more specific, for example:

```text
extract_web_results
```

The goal is to make the tool's purpose immediately clear to the model.

---

## 11. System Prompt Interaction

A strong tool description can still be undermined by conflicting system-prompt instructions.

Example:

```text
"always check customer details before proceeding"
```

This could cause customer-related queries to be routed to:

```text
get_customer
```

even when another tool is more appropriate.

### Therefore

After improving tool descriptions:

> **Review the system prompt for conflicting instructions.**

---

## 12. What to Do After Improving Descriptions

After updating the descriptions:

1. Test representative queries.
2. Check whether the correct tool is selected.
3. Look for remaining ambiguity.
4. Review system-prompt interactions.
5. Refine descriptions if necessary.

---

## 13. Tool Overload Qualification

There is an important qualification:

> **Description improvement is the fix when there are a workable number of tools and they are hard to distinguish.**

If the toolkit itself is overloaded, descriptions alone may not solve the problem.

The guide notes that beyond roughly **4–5 tools per agent**, selection can degrade because of decision complexity.

That is covered further in **Task 2.3**.

### Exam rule

```text
Few tools + confusing
        ↓
Improve descriptions

Too many tools
        ↓
Diagnose tool overload
```

---

## 14. Complete Tool Selection Model

Think:

```text
User request
     ↓
Claude understands intent
     ↓
Tool descriptions
     ↓
Purpose + Inputs + Examples
+ Edge cases + Boundaries
     ↓
Correct tool
```

---

## 15. Five Elements to Memorise

| Element | Question it answers |
|---|---|
| **Purpose** | What does this tool do? |
| **Inputs** | What does it need? |
| **Examples** | What kinds of requests use it? |
| **Edge cases** | What limitations/exceptions exist? |
| **Boundaries** | When should I use this instead of another tool? |

### Memory

> **P-I-E-E-B**

---

## 16. Exam Scenario

### Scenario

Two tools are frequently confused:

```text
get_customer
lookup_order
```

Claude often sends order queries to `get_customer`.

### Correct answer

> **Expand the descriptions of both tools with inputs, examples, edge cases, and explicit boundaries.**

Do not immediately add:

- A routing classifier
- Few-shot examples
- Another agent

---

## 17. Exam Traps

### Trap 1

**Few-shot examples are always the best fix.**

❌ Wrong.

Fix the descriptions first.

### Trap 2

**Add a routing classifier immediately.**

❌ Wrong.

Too much infrastructure for a simple ambiguity problem.

### Trap 3

**Consolidate all tools.**

❌ Wrong.

Consolidation may be unnecessary.

### Trap 4

**Ignore the system prompt.**

❌ Wrong.

System instructions can affect tool selection.

### Trap 5

**Create one huge generic tool.**

❌ Usually wrong.

Prefer narrow tools with clear responsibilities.

---

## 18. Build Exercise

Create ambiguous tools and test around **10 queries**.

Then:

1. Rewrite descriptions using the five elements.
2. Rerun the same queries.
3. Compare tool-selection accuracy.
4. Review the system prompt for conflicts.
5. Split broad tools where appropriate.

---

## 19. One-Minute Revision

```text
Tool selection
      ↓
Tool description
      ↓
Purpose
Inputs
Examples
Edge cases
Boundaries
      ↓
Better selection
```

### If weak descriptions:

> **Improve descriptions.**

### If too many tools:

> **Diagnose overload.**

---

## 20. Golden Rule

> **Tool descriptions are the primary mechanism for tool selection.**

When tools are confusing:

> **Improve descriptions first.**

When tools are too broad:

> **Split them into focused tools.**

When there are too many tools:

> **Consider tool distribution and consolidation.**

---

## 21. What to Memorise for the Exam

If you remember only **7 things**, remember these:

1. **Tool descriptions are the primary mechanism for tool selection.**
2. **Five elements: Purpose, Inputs, Examples, Edge cases, Boundaries.**
3. **Similar tools need explicit boundaries.**
4. **Weak descriptions → improve descriptions first.**
5. **Broad tools can be split into focused tools.**
6. **Confusing tool names can be renamed.**
7. **Always check for system-prompt conflicts.**

---

# Domain 2 — Task 2.2: Structured Error Responses

Here are **simple, exam-focused notes** for the page you shared.

---

## 1. What You Need to Know

When an MCP tool fails, the **error response must give the agent enough information to recover intelligently**.

A generic error like:

```text
Operation failed
```

is not useful because Claude cannot determine:

- What went wrong?
- Should it retry?
- Should it fix the input?
- Should it escalate?
- Should it request different permissions?

MCP provides the **`isError` flag** to tell the agent that the tool execution failed.

### Key exam rule

> **Good structured errors → intelligent recovery.**
>
> **Generic errors → blind recovery / wasted retries.**

---

## 2. The Four Error Categories

| Error Category | Meaning | Retry? | Recovery |
|---|---|---:|---|
| **Transient** | Temporary system problem | ✅ Yes | Retry after delay |
| **Validation** | Input is invalid | ❌ No | Correct input and call again |
| **Business** | Business rule/policy violation | ❌ No | Alternative workflow / escalation |
| **Permission** | Caller lacks access | ❌ No | Different credentials / escalation |

### Memory trick

**T → V → B → P**

> **Temporary → Validate → Business → Permission**

---

## 3. Transient Error

### Meaning

The request itself is valid, but the underlying service is temporarily unavailable.

Examples:

- Timeout
- Service unavailable
- Rate limit
- Temporary database outage

### Recovery

**Retry after a short delay.**

### Exam rule

> **Transient = retry.**

---

## 4. Validation Error

### Meaning

The request contains invalid input.

Examples:

- Missing required field
- Invalid ID format
- Invalid date
- Value outside allowed range

### Recovery

Claude should:

1. Understand the error.
2. Correct the input.
3. Send a **new corrected request**.

### Important exam point

`isRetryable: false` does **NOT** mean:

> "Give up."

It means:

> "Do not send the exact same request again."

### Memory

**Validation → Fix input → Call again**

---

## 5. Business Error

### Meaning

The request is technically valid, but it violates a business rule or policy.

Examples:

- Refund exceeds allowed limit
- Transaction exceeds account limit
- Operation violates company policy
- Business rule conflict

### Recovery

Do **not** retry the same request.

Instead:

- Follow an alternative workflow
- Escalate to a human
- Request approval

### Exam rule

> **Business error = same request will always fail.**

---

## 6. Permission Error

### Meaning

The caller does not have the required permissions.

Examples:

- Access denied
- Insufficient credentials
- Authorisation failure
- Service account lacks access

### Recovery

Use:

- Different credentials/principal, or
- Escalation to someone with the required access.

### Exam rule

> **Permission error ≠ retry the same request.**

---

## 7. `isRetryable` — Very Important

`isRetryable` answers **one specific question**:

> **"Will resending this exact request work?"**

| Error | `isRetryable` | What should Claude do? |
|---|---:|---|
| Transient | `true` | Retry same request |
| Validation | `false` | Fix input |
| Business | `false` | Alternative path / escalate |
| Permission | `false` | Use appropriate credentials |

---

## 8. Very Important Exam Trap

Do **not** interpret:

```text
isRetryable: false
```

as:

```text
STOP EVERYTHING
```

It means:

> **Don't resend the exact same call.**

The agent may still recover.

---

## 9. MCP `isError` vs Application Metadata

The MCP `CallToolResult` provides:

```text
content
structuredContent
isError
```

But fields such as:

```text
errorCategory
isRetryable
description
```

are **application-level conventions**, not MCP envelope fields.

They can be placed inside `structuredContent`.

### Exam understanding

```text
MCP
 └── isError
       ↓
Application metadata
 ├── errorCategory
 ├── isRetryable
 └── description
```

---

## 10. Access Failure vs Valid Empty Result

🔥 **This is one of the MOST IMPORTANT concepts in Task 2.2.**

### Access Failure

The tool **could not successfully query the data source**.

Examples:

- Database timeout
- Authentication failure
- Service unavailable
- Network failure

Therefore:

```json
"isError": true
```

### Valid Empty Result

The tool successfully queried the database.

But:

> **No matching data exists.**

This is **NOT an error**.

Therefore:

```json
"isError": false
```

and perhaps:

```text
"resultCount": 0
```

---

## 11. Easy Example

Suppose Claude searches for:

```text
Customer: john@example.com
```

### Situation A — Customer doesn't exist

```json
{
  "isError": false,
  "structuredContent": {
    "resultCount": 0
  }
}
```

Claude should:

❌ Not retry  
❌ Not escalate automatically  
✅ Tell the user no customer was found

### Situation B — Database timeout

```json
{
  "isError": true,
  "structuredContent": {
    "errorCategory": "transient",
    "isRetryable": true
  }
}
```

Claude should:

❌ Not treat it as "no customer found"  
✅ Retry after a delay

---

## 12. The Classic Exam Scenario

> A customer lookup returns an empty array. The agent retries three times and then escalates to a human. The customer account does not actually exist.

### Correct answer:

> **The tool does not distinguish between an access failure and a valid empty result.**

---

## 13. Error Propagation in Multi-Agent Systems

Use:

> **Local recovery + selective propagation**

### Step 1 — Recover locally

A subagent should handle recoverable transient errors itself.

### Step 2 — Propagate only unresolved errors

If retries fail, report failure to the coordinator.

### Step 3 — Include partial results

Don't simply say:

```text
Search failed.
```

Instead report successful results plus which sources failed.

---

## 14. Two Major Multi-Agent Anti-Patterns

### ❌ Anti-pattern 1 — Silently suppressing errors

```text
Search failed
↓
Return []
```

The coordinator thinks:

> "There are no results."

But actually:

> "The search failed."

### ❌ Anti-pattern 2 — Stop the entire workflow on one failure

One failed source should not automatically stop independent successful work.

---

## 15. Complete Recovery Model

```text
Tool call
   ↓
Did it fail?
   │
   ├── No → Valid result → Continue
   │
   └── Yes
        ↓
   Check errorCategory
        │
        ├── transient → Retry
        ├── validation → Fix input → Call again
        ├── business → Alternative workflow / escalate
        └── permission → Correct credentials / escalate
```

---

## 16. Error Metadata

A good structured error communicates:

### `errorCategory`

```text
transient
validation
business
permission
```

### `isRetryable`

Can the **same exact request** be sent again?

```text
true / false
```

### `description`

Explains:

- What happened
- Why it happened
- What should happen next

### Memory

> **Category → Retry decision → Explanation**

---

## 17. Generic Error vs Structured Error

### ❌ Bad

```text
Operation failed
```

### ✅ Good

```json
{
  "errorCategory": "business",
  "isRetryable": false,
  "description": "Refund exceeds the automatic refund limit. Manager approval is required."
}
```

Now Claude knows:

```text
Business error
      ↓
Don't retry
      ↓
Escalate / request approval
```

---

## 18. Exam Traps

### Trap 1 — Retry empty results

❌ Wrong.

`isError = false` means the query succeeded.

### Trap 2 — Generic error messages

❌ `"Operation failed"`

Use structured metadata.

### Trap 3 — Retry business errors

❌ Wrong.

A policy violation will still violate the policy.

### Trap 4 — Validation = retryable

❌ Wrong.

Fix the input first.

### Trap 5 — `isRetryable: false` means abandon

❌ Wrong.

Don't resend the same request unchanged.

### Trap 6 — Hide subagent failures as empty results

❌ Wrong.

The coordinator must know whether it was an empty result or an actual failure.

---

## 19. Practice Scenario — Correct Answer

A customer lookup returns an empty array and the customer simply does not exist.

### Correct response:

```json
{
  "isError": false,
  "structuredContent": {
    "resultCount": 0
  }
}
```

The agent knows:

> Query succeeded, but there are no matching records.

---

## 20. Build Exercise — What You Should Understand

Build structured errors for:

```text
Transient
↓
isError: true
isRetryable: true
↓
Retry
```

```text
Validation
↓
isError: true
isRetryable: false
↓
Fix input
```

```text
Business
↓
isError: true
isRetryable: false
↓
Alternative workflow / escalation
```

```text
Permission
↓
isError: true
isRetryable: false
↓
Different credentials / escalation
```

And separately:

```text
Valid empty result
↓
isError: false
↓
resultCount: 0
↓
No retry
```

---

## 21. One-Minute Revision

```text
MCP tool fails
      ↓
isError = true
      ↓
Read structured metadata
      ↓
errorCategory + isRetryable + description
```

| Category | Action |
|---|---|
| **Transient** | 🔄 Retry |
| **Validation** | 🔧 Fix input |
| **Business** | 🚨 Alternative path / escalate |
| **Permission** | 🔐 Correct credentials / escalate |

### Most important distinction:

```text
Access failure
    ↓
isError = true
    ↓
Possible retry
```

versus:

```text
Valid empty result
    ↓
isError = false
    ↓
No retry
```

---

## 22. Golden Rules ⭐

### Rule 1

> **Transient → Retry**

### Rule 2

> **Validation → Fix input**

### Rule 3

> **Business → Alternative workflow / escalation**

### Rule 4

> **Permission → Correct credentials / escalation**

### Rule 5

> **Empty result ≠ error**

### Rule 6

> **`isRetryable` means "Can I resend this exact request?"**

### Rule 7

> **Never hide a subagent failure as an empty successful result.**

### Rule 8

> **Recover locally first; propagate unresolved failures to the coordinator.**

---

## 23. What to Memorise for the Exam

If you remember only **7 things**, remember these:

1. **MCP `isError: true` indicates tool failure.**
2. **Transient → retry.**
3. **Validation → fix the input, then call again.**
4. **Business → don't retry; use another workflow/escalate.**
5. **Permission → don't retry blindly; use appropriate credentials/escalate.**
6. **Successful empty result → `isError: false`; don't retry.**
7. **Subagents should recover locally and propagate only unresolved failures, including partial results.**

**Best memory sentence:**

> **Retry temporary problems, fix bad inputs, escalate policy/access problems, and never confuse "nothing found" with "couldn't search."**

---

# Domain 2 — Task 2.3: Tool Distribution & Tool Choice

Here are **simple, exam-focused notes** for the page you shared.

---

## 1. What You Need to Know

The number of tools given to an agent directly affects how reliably it selects the correct tool.

This is **not just an implementation detail**. It is an **architecture decision**.

### Key exam rule

> **Give each agent only the tools required for its role.**

The guide recommends approximately **4–5 tools per agent**.

---

## 2. The Tool Overload Problem

Suppose one agent has:

```text
18 tools
```

The agent must decide between many possible tools.

As the number of tools increases:

```text
More tools
    ↓
More decision complexity
    ↓
More tool-selection errors
    ↓
Less reliable agent
```

### Better architecture

Instead of:

```text
One Agent
 ├── 18 tools
```

Use specialised agents:

```text
Web Search Agent
 ├── 4 tools

Document Agent
 ├── 4 tools

Synthesis Agent
 ├── 4 tools
```

### Memory

> **4–5 tools per agent, scoped to its role.**

---

## 3. Tool Relevance Is Just as Important as Tool Count

Having only 4–5 tools is not enough.

The tools must also be **relevant to the agent's role**.

### Bad design

```text
Synthesis Agent
 ├── compile_report
 ├── format_citation
 ├── web_search
 └── fetch_page
```

The synthesis agent might decide:

> "I need more information, so I'll search the web myself."

That can cause:

- duplicated work
- unnecessary tool calls
- wasted context
- incorrect workflow behaviour

### Better design

```text
Synthesis Agent
 ├── compile_report
 ├── verify_fact
 ├── format_citation
 └── assess_coverage
```

The synthesis agent concentrates on **synthesis**, not web research.

---

## 4. Principle: Role-Based Tool Distribution

The core principle is:

> **Each agent gets only the tools it needs for its defined role. Nothing more.**

Think:

```text
Agent role
    ↓
Required capabilities
    ↓
Required tools only
```

### Example

| Agent | Appropriate tools |
|---|---|
| Web Search | `search_web`, `fetch_page`, `extract_links`, `save_snippet` |
| Document Analysis | `extract_metadata`, `extract_data_points`, `summarize_content`, `verify_claim` |
| Synthesis | `compile_report`, `verify_fact`, `format_citation`, `assess_coverage` |
| Coordinator | `Agent`, `review_output`, `request_revision` |

---

## 5. But Don't Always Split Tools

🔥 **This is an important exam distinction.**

Tool overload does **not always mean "create more agents."**

Suppose you have:

```text
22 tools
```

and 19 of them are variations of the same job:

```text
pivot_table
calculate_percentile
normalise_currency
...
```

These are all variations of:

> Transform data.

If you create a separate transformation agent with 19 tools, you have simply moved the overload problem.

---

## 6. Consolidating Near-Duplicate Tools

If many tools perform the **same type of operation** and share a similar input/output shape, consolidate them.

Instead of:

```text
pivot_table
calculate_percentile
normalise_currency
...
```

Create:

```text
transform_data
```

with:

```text
transform_type
```

Now:

```text
22 tools
   ↓
4 tools
```

The capabilities are still available.

The model simply chooses:

```text
transform_data
       ↓
transform_type = "pivot"
```

instead of choosing between 19 separate transformation tools.

---

## 7. The Most Important Decision Table

🔥 **Memorise this table for the exam.**

| Situation | Correct fix |
|---|---|
| Few tools but two descriptions are confusing | **Improve descriptions** |
| Different jobs | **Split by role** |
| Many variations of the same job | **Consolidate into one parameterised tool** |
| Tool can do more than agent should be allowed to do | **Constrain the tool** |
| 22 tools split across two MCP servers | ❌ **Moving servers does not solve overload** |

### Easy memory:

> **Confusing → Describe**
>
> **Different jobs → Split**
>
> **Same job → Consolidate**
>
> **Too powerful → Constrain**

---

## 8. Task 2.1 vs Task 2.3

### Task 2.1

Problem:

```text
get_customer
lookup_order
```

Only a few tools exist, but Claude selects the wrong one.

### Fix:

> **Improve tool descriptions.**

### Task 2.3

Problem:

```text
22 tools
```

Claude struggles to choose correctly.

### Fix:

> **Reduce decision complexity through distribution or consolidation.**

### Exam rule

> **Count the tools before choosing the remedy.**

---

## 9. Moving Tools to Another MCP Server Does NOT Solve Overload

Suppose:

```text
MCP Server A
 ├── 11 tools

MCP Server B
 ├── 11 tools
```

The selection problem still exists if the model receives all available tools.

### Therefore:

> **Changing MCP server boundaries is not the solution to tool overload.**

---

## 10. `tool_choice` Configuration

The `tool_choice` parameter controls **how Claude interacts with tools**.

There are three important modes:

```text
auto
any
forced selection
```

---

## 11. `tool_choice: "auto"`

### Meaning

Claude decides:

> Should I call a tool or respond with normal text?

### Use when:

- normal agent operation
- tool use is optional
- Claude needs flexibility

### Memory

> **Auto = Claude decides.**

---

## 12. `tool_choice: "any"`

### Meaning

Claude **must call a tool**, but Claude chooses **which tool**.

### Important distinction

`any` does **NOT** mean:

> "Use any tool randomly."

It means:

> **A tool call is mandatory, but the model chooses which available tool.**

---

## 13. When to Use `"any"`

Use it when a tool call is mandatory but Claude should choose the appropriate tool.

For example:

```text
Invoice
Receipt
Contract
```

with:

```text
extract_invoice
extract_receipt
extract_contract
```

Then:

```text
tool_choice = any
```

ensures Claude makes a tool call.

---

## 14. Forced Tool Selection

Sometimes Claude should not choose freely.

You need to force a **specific tool**.

Example:

```json
{
  "tool_choice": {
    "type": "tool",
    "name": "extract_metadata"
  }
}
```

Meaning:

```text
Claude MUST call:
extract_metadata
```

---

## 15. When to Use Forced Selection

Use forced selection when a particular operation is a **mandatory workflow step**.

Example:

```text
Document
   ↓
extract_metadata
   ↓
extract_data_points
   ↓
summarize_content
```

After the mandatory step:

```text
tool_choice = auto
```

can be used for remaining workflow steps.

---

## 16. `auto` vs `any` vs Forced

| Mode | What happens? | Main use |
|---|---|---|
| **auto** | Claude may call a tool or return text | Normal operation |
| **any** | Claude MUST call a tool, but chooses which | Guaranteed tool output |
| **forced** | Claude MUST call one specific tool | Mandatory workflow step |

### Memory trick

> **Auto = optional**
>
> **Any = required, model chooses**
>
> **Forced = required, you choose**

---

## 17. Scoped Cross-Role Tools

Sometimes an agent needs **occasional access to a capability belonging to another role**.

The naive approach is:

```text
Synthesis Agent
       ↓
Coordinator
       ↓
Search Agent
       ↓
Coordinator
       ↓
Synthesis Agent
```

This creates extra round trips.

---

## 18. Why Coordinator Round Trips Are Expensive

Suppose a synthesis agent needs to verify a simple fact.

Naive workflow:

```text
Synthesis
   ↓
Coordinator
   ↓
Search Agent
   ↓
Coordinator
   ↓
Synthesis
```

That's:

> **2–3 additional hops.**

For frequent simple operations, this adds unnecessary latency.

The guide gives a scenario where **85%** of verifications are simple lookups.

---

## 19. Correct Solution: Scoped Cross-Role Tool

Give the synthesis agent a **limited version** of the verification capability.

For example:

```text
Synthesis Agent
 ├── compile_report
 ├── format_citation
 ├── assess_coverage
 └── verify_fact
```

But `verify_fact` is **scoped**.

It handles:

```text
Simple single-source lookup
```

while complex verification still goes through the coordinator.

---

## 20. Simple vs Complex Verification

### Simple

```text
"What year was this company founded?"
```

Use:

```text
verify_fact
```

directly.

### Complex

```text
"Verify this claim using five independent sources and reconcile conflicting evidence."
```

Use:

```text
Synthesis
   ↓
Coordinator
   ↓
Research/Search Agent
```

### Memory

> **Simple → local scoped tool**
>
> **Complex → coordinator**

---

## 21. Why Not Give the Full Search Tool?

Because of **least privilege**.

Don't give:

```text
web_search
```

if the synthesis agent only needs:

```text
simple fact verification
```

Instead give:

```text
verify_fact
```

with a narrow scope.

This:

- reduces misuse
- reduces unnecessary tool choices
- reduces latency
- makes the agent's role clearer
- follows least-privilege design

---

## 22. Generic Tool vs Constrained Tool

### Generic tool

```text
fetch_url
```

can potentially fetch:

```text
anything
from anywhere
```

That's too much capability for an agent that only needs documents.

### Better

```text
load_document
```

which:

- accepts document URLs
- validates allowed URLs
- rejects unrelated resources

### Architecture

```text
fetch_url
   ↓
Too broad ❌

load_document
   ↓
Document URLs only ✅
```

---

## 23. Principle of Least Privilege

> **Give an agent exactly the capability it needs — and nothing more.**

Instead of:

```text
Agent → fetch anything
```

use:

```text
Agent → load required documents only
```

### Benefits

1. Prevents misuse
2. Reduces accidental side effects
3. Makes tool purpose clearer
4. Reduces tool-selection ambiguity
5. Improves security

---

## 24. Complete Example: Good Multi-Agent Tool Distribution

### Web Search Agent

```text
search_web
fetch_page
extract_links
save_snippet
```

### Document Analysis Agent

```text
extract_metadata
extract_data_points
summarize_content
verify_claim
```

### Synthesis Agent

```text
compile_report
verify_fact       ← scoped
format_citation
assess_coverage
```

### Coordinator

```text
Agent
review_output
request_revision
```

Each agent has a focused toolset.

---

## 25. Complete Decision Framework

When you see a tool-distribution question in the exam, think in this order:

```text
How many tools?
      ↓
Are they relevant to the agent's role?
      ↓
Are they different jobs or variations of one job?
      ↓
Is the capability too broad?
      ↓
Does workflow order need enforcement?
```

Then choose:

```text
Few + confusing
    → Improve descriptions

Different jobs
    → Split by role

Same job / similar shape
    → Consolidate

Too powerful
    → Constrain

Mandatory specific first step
    → Forced tool_choice

Must call some tool
    → any

Tool optional
    → auto

Frequent simple cross-role operation
    → Scoped cross-role tool
```

---

## 26. Exam Traps

### Trap 1 — Give one agent all tools

```text
18 tools → one agent
```

❌ Wrong.

Use role-specific distribution.

### Trap 2 — Create another MCP server

```text
22 tools
↓
11 tools/server
```

❌ Not sufficient.

The model can still receive all available tools.

### Trap 3 — Always create another agent

❌ Wrong.

If 19 tools are variations of the same job, **consolidate them**.

### Trap 4 — Use `"auto"` for mandatory structured output

❌ Wrong.

Use:

```text
any
```

when any tool call is required.

### Trap 5 — Use `"any"` when one specific tool is mandatory

❌ Wrong.

Use **forced selection**.

### Trap 6 — Route every simple verification through coordinator

❌ Inefficient.

Use a scoped cross-role tool for frequent simple cases.

### Trap 7 — Give `fetch_url` to an agent that only needs documents

❌ Too broad.

Use:

```text
load_document
```

with appropriate validation.

---

## 27. Practice Scenario

A synthesis agent frequently sends simple fact-verification requests to the coordinator.

The analysis shows:

```text
85% = simple lookups
15% = complex verification
```

### Correct answer

> **Give the synthesis agent a scoped `verify_fact` tool for simple lookups and route only complex verification through the coordinator.**

Why?

```text
85% simple
   ↓
Local verify_fact
   ↓
Fast

15% complex
   ↓
Coordinator
   ↓
Full research workflow
```

**Answer: Option A.**

---

## 28. Build Exercise — What You Should Understand

The lesson's build exercise tests five things:

### 1. Role-specific distribution

Create:

```text
Web Search
Document Analysis
Synthesis
```

with approximately **4–5 tools each**.

### 2. Scoped cross-role tool

Add:

```text
verify_fact
```

to the synthesis agent for simple lookups.

### 3. Forced workflow

Make:

```text
extract_metadata
```

the mandatory first tool for document analysis.

Then switch to:

```text
auto
```

for subsequent calls.

### 4. Least privilege

Replace:

```text
fetch_url
```

with:

```text
load_document
```

that validates document URLs.

### 5. End-to-end testing

Verify that:

```text
Web Search Agent
    ↓
only web tools

Document Agent
    ↓
extract_metadata first

Synthesis Agent
    ↓
compile_report + scoped verify_fact
```

No agent should use tools outside its assigned scope.

---

## 29. One-Minute Revision

### Tool Distribution

```text
Too many tools
      ↓
Selection becomes unreliable
      ↓
Reduce decision complexity
```

### Solutions

```text
Few + confusing
→ Improve descriptions

Different jobs
→ Split by role

Same job / similar shape
→ Consolidate

Too much capability
→ Constrain

Frequent simple cross-role task
→ Scoped cross-role tool
```

### `tool_choice`

```text
auto
→ Tool optional

any
→ Tool mandatory, Claude chooses

forced
→ Specific tool mandatory
```

### Security

```text
Generic capability
      ↓
Too much power
      ↓
Constrained capability
      ↓
Least privilege
```

---

## 30. Golden Rules ⭐

### Rule 1

> **Aim for roughly 4–5 relevant tools per agent.**

### Rule 2

> **Tool count and tool relevance both matter.**

### Rule 3

> **Different jobs → split by role.**

### Rule 4

> **Many variations of the same job → consolidate into a parameterised tool.**

### Rule 5

> **Don't expect MCP server boundaries to solve model tool overload.**

### Rule 6

> **`auto` = optional tool use.**

### Rule 7

> **`any` = must use a tool, model chooses which.**

### Rule 8

> **Forced selection = must use a specific tool.**

### Rule 9

> **Frequent simple cross-role operations → give the agent a scoped local tool.**

### Rule 10

> **Complex cross-role operations → route through the coordinator.**

### Rule 11

> **Replace generic tools with constrained alternatives when possible.**

### Rule 12

> **Least privilege: give the agent exactly what it needs, nothing more.**

---

## 31. What to Memorise for the Exam

If you remember only **8 things**, remember these:

1. **4–5 tools per agent** is the recommended range.
2. **Role-specific tools** prevent cross-role misuse.
3. **Few confusing tools → improve descriptions.**
4. **Different jobs → split by role.**
5. **Same job with many variations → consolidate.**
6. **`auto` / `any` / forced = optional / any-tool-required / specific-tool-required.**
7. **Simple frequent cross-role work → scoped cross-role tool.**
8. **Generic capability → constrain it using least privilege.**

### 🧠 Final memory sentence

> **Count the tools, match them to the role, consolidate similar jobs, constrain excessive power, and use `auto`, `any`, or forced selection according to how much tool-choice freedom the workflow requires.**

---

# Domain 2 — Task 2.4: MCP Server Integration

Here are **simple, exam-focused notes** for the page you shared. I’ll keep the same structure and level of detail as the previous Domain 2 notes.

---

## 1. What You Need to Know

**MCP (Model Context Protocol) servers** extend Claude's capabilities by connecting it to external systems such as:

- Databases
- APIs
- GitHub
- Jira
- Slack
- Development tools
- Internal company systems

The key exam areas are:

1. **MCP configuration scope**
2. **Environment variables**
3. **MCP resources**
4. **Community server vs custom server**
5. **MCP tool descriptions**
6. **Security and secrets**

---

## 2. MCP Configuration Scoping

The exam guide focuses on **two configuration levels**:

| Level | File | Shared? | Main use |
|---|---|---|---|
| **Project-level** | `.mcp.json` | ✅ Team | Team-wide MCP servers |
| **User-level** | `~/.claude.json` | ❌ Personal | Personal/experimental servers |

### Easy memory

> **Project = Team**
>
> **User = Personal**

---

## 3. Project-Level `.mcp.json`

The `.mcp.json` file lives in the **project repository root**.

Example:

```json
{
  "mcpServers": {
    "github": {
      "type": "http",
      "url": "https://api.githubcopilot.com/mcp/"
    },
    "filesystem": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "${WORKSPACE_ROOT:-.}"
      ]
    }
  }
}
```

Because it is inside the project:

```text
Project
│
├── .mcp.json
├── src/
├── tests/
└── README.md
```

it can be:

- committed to Git
- version-controlled
- shared with teammates
- automatically used by everyone working on the project

### Use `.mcp.json` for:

- Jira
- GitHub
- Internal APIs
- Shared databases
- Team development tools

---

## 4. User-Level `~/.claude.json`

This configuration belongs to the individual developer.

```text
~/.claude.json
```

It is:

- Personal
- Not team-shared
- Not normally version-controlled
- Useful for experiments and personal integrations

### Example

You are testing a new MCP server before recommending it to your team.

Use:

```text
~/.claude.json
```

instead of:

```text
.mcp.json
```

### Memory

> **Team server → `.mcp.json`**
>
> **Personal experiment → `~/.claude.json`**

---

## 5. Important Exam Qualification

The current Claude Code documentation has evolved to support **three scopes**:

- `local`
- `project`
- `user`

However, the **exam guide expects the two-level project-vs-user distinction**.

So for the exam:

> **Team-wide → Project `.mcp.json`**
>
> **Personal → User `~/.claude.json`**

---

## 6. Remote vs Local MCP Servers

### Remote MCP Server

A remote server uses:

```json
{
  "type": "http",
  "url": "https://example.com/mcp"
}
```

Meaning:

```text
Claude
  ↓
HTTP
  ↓
Remote MCP Server
```

### Local MCP Server

A local server uses:

```json
{
  "command": "npx",
  "args": ["-y", "some-mcp-server"]
}
```

Meaning the server is started locally and communicates through **stdio**.

```text
Claude Code
    ↓
Local process
    ↓
stdio
    ↓
MCP Server
```

### Memory

> **Remote = `type` + `url`**
>
> **Local = `command` + `args`**

---

## 7. Important Configuration Trap

Suppose you write:

```json
{
  "url": "https://example.com/mcp"
}
```

but forget:

```json
"type": "http"
```

❌ This is a configuration error.

Correct:

```json
{
  "type": "http",
  "url": "https://example.com/mcp"
}
```

### Exam memory

> **Remote URL → don't forget `"type": "http"`**

---

## 8. All Configured Tools Become Available

An important concept:

> MCP servers don't require a separate manual activation step once they are configured and reachable.

If multiple servers are configured:

```text
.mcp.json
   ↓
GitHub tools
Jira tools
Filesystem tools
```

and user configuration also contains:

```text
~/.claude.json
   ↓
Personal tools
```

the available tools can be discovered together.

### Important connection to Task 2.3

This is why simply putting tools on different MCP servers does **not necessarily solve tool overload**.

For example:

```text
Server A → 10 tools
Server B → 10 tools

Total available → 20 tools
```

The model may still face a large tool-selection problem.

---

## 9. Environment Variable Expansion

🔥 **Very important exam topic.**

Never put credentials directly into `.mcp.json`.

### ❌ Bad

```json
{
  "env": {
    "GITHUB_TOKEN": "ghp_123456789"
  }
}
```

The secret is now inside the repository configuration.

That can cause:

- credential leakage
- Git history exposure
- security incidents

---

## 10. Correct Approach

Use:

```json
{
  "env": {
    "GITHUB_TOKEN": "${GITHUB_TOKEN}",
    "DATABASE_URL": "${DATABASE_URL}"
  }
}
```

The configuration contains the **variable name**, not the secret.

Each developer sets the value locally.

```text
Developer A
GITHUB_TOKEN = token_A

Developer B
GITHUB_TOKEN = token_B
```

Both use the same:

```text
.mcp.json
```

but different credentials.

### Memory

> **Configuration is shared; credentials are local.**

---

## 11. Benefits of Environment Variables

Using:

```text
${GITHUB_TOKEN}
```

provides several benefits:

### 1. Safe configuration

The actual secret isn't committed.

### 2. Individual credentials

Each developer can authenticate independently.

### 3. Easy token rotation

Changing the token doesn't require changing `.mcp.json`.

### 4. No secrets in Git history

The repository contains:

```text
${GITHUB_TOKEN}
```

rather than:

```text
actual-secret-value
```

### Memory

> **Configuration is shared; credentials are local.**

---

## 12. Environment Variable Defaults

You can also use:

```text
${VARIABLE:-default}
```

Meaning:

```text
If VARIABLE exists
    ↓
use VARIABLE

Otherwise
    ↓
use default
```

Example:

```text
${WORKSPACE_ROOT:-.}
```

means:

> Use `WORKSPACE_ROOT` if defined; otherwise use the current directory `.`.

### Memory

> **`${VAR}` = required reference**
>
> **`${VAR:-default}` = variable with fallback**

---

## 13. Important Current Behaviour

The guide notes that an unset environment variable **does not necessarily stop the entire configuration from loading**.

Claude Code can warn and continue when a variable without a default is missing.

Therefore:

> Don't rely on a missing environment variable to fail loudly and safely.

The main exam concept is:

> **Use environment variables instead of hard-coded credentials.**

---

## 14. MCP Resources

🔥 Another important exam concept.

MCP provides more than just tools.

It also provides **resources**.

### Tools

Tools allow the agent to:

> **Take action.**

Examples:

```text
search_issues
create_issue
update_issue
```

### Resources

Resources allow the agent to access information/catalogues.

Examples:

```text
Database schema
Documentation hierarchy
Issue summaries
```

### Memory

> **Tools = Act**
>
> **Resources = Know what data exists**

---

## 15. Why MCP Resources Matter

Suppose Claude needs to understand a database.

### Without a resource

Claude may need to:

```text
list_tables
    ↓
describe_table
    ↓
describe_table
    ↓
describe_table
    ↓
...
```

This causes:

- unnecessary tool calls
- additional latency
- additional context
- wasted tokens

### With a schema resource

Claude can receive a database schema such as:

```text
Customers
 ├── id
 ├── name
 └── email

Orders
 ├── id
 ├── customer_id
 └── amount
```

Now Claude already understands the available structure.

### Result

```text
Fewer exploratory calls
        ↓
Less latency
        ↓
Less context usage
```

---

## 16. Examples of MCP Resources

Examples include:

### Issue summaries

```text
Jira issue list
 ├── ISSUE-101 → Open
 ├── ISSUE-102 → In Progress
 └── ISSUE-103 → Closed
```

### Documentation hierarchy

```text
Internal Docs
 ├── Authentication
 ├── Payments
 ├── APIs
 └── Deployment
```

### Database schema

```text
Users
Orders
Products
Payments
```

These resources give the agent **visibility into available information**.

---

## 17. Important MCP Resource Qualification

The guide's exam framing is:

> Resources expose content catalogues to agents.

A precision point from the current MCP specification is that **resources are application-controlled**.

The server lists resources, but the client/host determines when they are attached to the model's context.

### For the exam

Remember:

> **Resources reduce exploratory tool calls by exposing available data.**

---

## 18. Build vs Use an MCP Server

🔥 **Very important exam decision.**

Suppose your company needs Jira integration.

You have two choices:

```text
Option A
Use existing community MCP server

Option B
Build custom MCP server
```

What should you do first?

> **Evaluate the existing community MCP server.**

---

## 19. When to Use Community MCP Servers

Use an existing community server when:

- integration is standard
- common workflows are sufficient
- maintained implementation exists
- you don't need special business logic

Examples:

```text
Jira
GitHub
Slack
Linear
Notion
```

### Why?

Because you save:

- Development time
- Testing effort
- Maintenance effort
- Long-term engineering cost

### Exam rule

> **Standard integration → evaluate community server first.**

---

## 20. When to Build a Custom MCP Server

Build a custom server when the scenario specifically requires something existing servers cannot provide.

For example:

### 1. Proprietary system

Your company has an internal system with no suitable MCP server.

### 2. Custom business logic

Your organisation requires special rules embedded directly into the tool layer.

### 3. Team-specific workflow

The existing server cannot support the workflow your team requires.

### Exam rule

> **Specific requirements that existing servers cannot satisfy → custom MCP server.**

---

## 21. Build vs Use Decision Table

| Scenario | Correct choice |
|---|---|
| Standard Jira integration | ✅ Community server |
| Standard GitHub integration | ✅ Community server |
| Standard Slack integration | ✅ Community server |
| Existing server satisfies requirements | ✅ Use it |
| Proprietary internal system | ✅ Custom |
| Special company workflow unsupported by community server | ✅ Custom |
| Custom business logic required | ✅ Custom |

### Golden exam principle

> **Don't build what you can reliably reuse.**

---

## 22. MCP Tool Descriptions

This connects directly to **Task 2.1**.

Suppose an MCP server exposes:

```text
search_codebase
```

with this description:

```text
"Searches code"
```

That is too weak.

Claude may prefer a built-in tool such as:

```text
Grep
```

because Claude has richer information about what Grep does.

---

## 23. Why Claude May Prefer Built-In Tools

Imagine:

```text
MCP tool:
search_codebase
"Searches code"
```

versus:

```text
Grep:
Search files using exact text patterns,
regular expressions, file filters, etc.
```

Claude has more useful information about the built-in tool.

Therefore:

```text
Sparse MCP description
        ↓
Claude doesn't understand its advantage
        ↓
Built-in tool selected
```

This doesn't necessarily mean the MCP tool is worse.

It means its **description is insufficient**.

---

## 24. Correct Fix: Enhance the Description

Instead of:

```text
search_codebase:
"Searches code"
```

write something like:

```text
search_codebase:
"Performs semantic code search across the entire repository
using AST-aware indexing. Returns matching functions, classes,
and methods with file paths, line numbers, and surrounding
code. More accurate than text-based grep for finding code by
intent rather than exact string match. Use this instead of
Grep when searching for code by what it does rather than
what it contains."
```

Now Claude understands:

- What it does
- What it returns
- Why it is useful
- When to use it
- When it is better than the alternative

---

## 25. Connection to Task 2.1

### Task 2.1

Weak tool descriptions cause:

```text
Wrong MCP tool selection
```

### Task 2.4

Sparse MCP descriptions can cause:

```text
Built-in tool selected instead of MCP tool
```

### Same underlying principle

> **Claude needs strong tool descriptions to make good tool-selection decisions.**

So:

```text
Better description
      ↓
Better capability understanding
      ↓
Better tool selection
```

---

## 26. Exam Traps

### Trap 1 — Build custom Jira MCP server immediately

❌ Wrong.

First:

> Evaluate existing community MCP servers.

### Trap 2 — Put team configuration in `~/.claude.json`

❌ Wrong.

Use:

```text
.mcp.json
```

for team-wide configuration.

### Trap 3 — Put personal experiments in `.mcp.json`

❌ Usually wrong.

Use:

```text
~/.claude.json
```

for personal/experimental configuration.

### Trap 4 — Commit API keys into `.mcp.json`

❌ Security problem.

Use:

```text
${GITHUB_TOKEN}
```

instead.

### Trap 5 — Use URL without `type`

❌ Wrong for a remote HTTP server.

Use:

```json
{
  "type": "http",
  "url": "https://..."
}
```

### Trap 6 — Assume resources are tools

❌ Wrong.

```text
Tools → perform actions
Resources → expose information/content
```

### Trap 7 — Assume an MCP server automatically wins against built-in tools

❌ Wrong.

If its description is poor, Claude may prefer the built-in tool.

Improve the MCP tool description.

---

## 27. Practice Scenario

### Question

A team needs Jira integration for Claude Code.

A developer proposes:

> "Let's build our own MCP server from scratch."

What should the team do first?

### Correct answer:

> **Evaluate existing community MCP servers for Jira and only build a custom server if they cannot satisfy the team's specific requirements.**

### Why?

Jira is a standard integration.

Therefore:

```text
Standard integration
       ↓
Check existing server
       ↓
Does it satisfy requirements?
       │
       ├── Yes → Use it
       │
       └── No → Build custom
```

**Correct answer: Option A.**

---

## 28. Build Exercise — What You Should Understand

The lesson's build exercise covers **five important tasks**.

### 1. Create `.mcp.json`

Put a team-wide MCP server in the project root.

```text
project/
└── .mcp.json
```

### 2. Use environment variables

Instead of:

```text
GITHUB_TOKEN = actual-secret
```

use:

```text
GITHUB_TOKEN = ${GITHUB_TOKEN}
```

### 3. Configure a personal server

Put an experimental/personal MCP server in:

```text
~/.claude.json
```

### 4. Expose an MCP resource

For example:

```text
db://schema/main
```

containing:

```text
table
column
relationship
```

information.

### 5. Improve MCP tool descriptions

Descriptions should explain:

1. What the tool does
2. What it returns
3. When to use it
4. Why it is better than alternatives when relevant

The guide suggests descriptions of around **3–5 sentences** for this purpose.

---

## 29. One-Minute Revision

## MCP Configuration

```text
Team
 ↓
.mcp.json

Personal
 ↓
~/.claude.json
```

## Server types

```text
Remote
 ↓
type: http
url: ...

Local
 ↓
command + args
 ↓
stdio
```

## Credentials

```text
❌ Hard-coded secret

✅ ${GITHUB_TOKEN}
```

## Resources

```text
Resources
 ↓
Expose available information
 ↓
Reduce exploratory tool calls
```

## Server choice

```text
Standard integration
 ↓
Community server first

Special internal requirement
 ↓
Custom MCP server
```

## Tool descriptions

```text
Sparse description
 ↓
Poor tool selection

Detailed description
 ↓
Better tool selection
```

---

## 30. Golden Rules ⭐

### Rule 1

> **Project `.mcp.json` = team-wide configuration.**

### Rule 2

> **User `~/.claude.json` = personal/experimental configuration.**

### Rule 3

> **Remote MCP = `type: "http"` + `url`.**

### Rule 4

> **Local MCP = `command` + `args` and stdio.**

### Rule 5

> **Never commit credentials directly into version-controlled MCP configuration.**

### Rule 6

> **Use `${ENV_VAR}` for credentials.**

### Rule 7

> **Resources expose information; tools perform actions.**

### Rule 8

> **Standard integration → evaluate community MCP servers first.**

### Rule 9

> **Custom MCP → only when specific requirements cannot be satisfied by existing servers.**

### Rule 10

> **Sparse MCP tool descriptions can cause Claude to prefer built-in tools.**

### Rule 11

> **Improve MCP tool descriptions with capabilities, outputs, use cases, and useful comparisons.**

---

## 31. What to Memorise for the Exam

If you remember only **10 things**, remember these:

1. **`.mcp.json` → project/team configuration.**
2. **`~/.claude.json` → personal configuration.**
3. **Remote server → `type: "http"` + `url`.**
4. **Local server → `command` + `args`.**
5. **Never hard-code secrets in version-controlled MCP configuration.**
6. **Use `${ENV_VAR}` for credentials.**
7. **Resources expose data/catalogues; tools perform actions.**
8. **Standard integration → community server first.**
9. **Custom server → only for unsupported proprietary/team-specific requirements.**
10. **Weak MCP tool description → Claude may prefer a built-in tool; improve the description first.**

### 🧠 Final memory sentence

> **Team servers go in `.mcp.json`, personal servers go in `~/.claude.json`, secrets stay in environment variables, resources expose information, community servers come before custom builds, and strong tool descriptions help MCP tools compete with built-in tools.**

---

# Domain 2 — Task 2.5: Built-in Tools

Here are **simple, exam-focused notes** for the page you shared. This task is heavily focused on knowing **which built-in Claude Code tool to use for which job**, especially **Grep vs Glob**, **Edit vs Read + Write**, and **incremental codebase exploration**.

---

## 1. What You Need to Know

Claude Code provides six important built-in tools for working with codebases:

| Tool | Main purpose |
|---|---|
| **Read** | Read file contents |
| **Write** | Create or replace complete file contents |
| **Edit** | Make targeted changes to existing text |
| **Bash** | Run shell commands |
| **Grep** | Search inside file contents |
| **Glob** | Find files by path/name pattern |

### Most important exam distinction

> **Grep = search INSIDE files**
>
> **Glob = find FILES by their names/paths**

---

## 2. Grep vs Glob — ⭐ MOST IMPORTANT

This is the distinction you should memorise first.

### Grep

**Grep searches file contents.**

Use Grep when you want to find:

- Function callers
- Error messages
- Import statements
- Variable assignments
- Specific text
- References to a class/function

Example:

```text
Grep: "processLegacyOrder"
```

This finds files containing:

```text
processLegacyOrder(...)
```

### Memory

> **Grep → What is INSIDE the file?**

---

## 3. Glob

**Glob searches file paths/names using patterns.**

Use Glob when you want to find:

- Test files
- Configuration files
- Files with a particular extension
- Files in a directory
- Files matching a naming convention

Examples:

```text
**/*.test.tsx
```

Finds test files.

```text
**/config.*
```

Finds configuration files.

```text
content/domains/**/*.mdx
```

Finds MDX files under the domains directory.

### Memory

> **Glob → Which FILES exist?**

---

## 4. Grep vs Glob — Easy Table

| Question | Tool |
|---|---|
| Which files contain `processOrder`? | **Grep** |
| Which files contain `"timeout"`? | **Grep** |
| Which files import `utils/auth`? | **Grep** |
| Where is `processOrder()` called? | **Grep** |
| Find all `.test.tsx` files | **Glob** |
| Find all `config.*` files | **Glob** |
| Find all TypeScript files in a directory | **Glob** |
| Find files matching a naming pattern | **Glob** |

### One-line memory:

> **Grep = content. Glob = path.**

---

## 5. Classic Exam Trap

### Question

> Find every file that calls `processLegacyOrder()`.

❌ Wrong:

```text
Glob: "**/*processLegacyOrder*"
```

Why?

Glob searches **file paths**, not file contents.

### Correct:

```text
Grep: "processLegacyOrder"
```

Because you're looking for the function name **inside files**.

---

## 6. Another Exam Trap

### Question

> Find all test files ending in `.test.tsx`.

❌ Wrong:

```text
Grep: ".test.tsx"
```

### Correct:

```text
Glob: "**/*.test.tsx"
```

Because you're searching for files based on their **path/name pattern**.

---

## 7. Read, Write and Edit

These three tools handle file modifications and inspection.

Think:

```text
Read  → Look
Edit  → Change part
Write → Replace/create whole file
```

---

## 8. Read

**Read loads file contents into the context.**

Use it when you need to:

- Understand code
- Inspect a function
- Follow imports
- Understand how something works
- Examine a file discovered by Grep/Glob

Example:

```text
Grep
  ↓
Find OrderProcessor.ts
  ↓
Read
  ↓
Understand the code
```

### Important principle

Don't Read every file immediately.

First discover which files matter.

---

## 9. Edit

**Edit performs targeted modifications.**

You provide:

```text
old_string
new_string
```

Example:

```text
old_string:
function processOrder(id: string)

new_string:
function processOrder(id: string, validate: boolean = true)
```

Edit changes the matching text without loading/replacing the entire file.

### Why Edit is preferred

- Precise
- Fast
- Lower context usage
- Smaller modification scope

### Memory

> **Small targeted change → Edit**

---

## 10. What Happens If Edit Finds Multiple Matches?

Suppose you ask Edit to replace:

```text
old_string:
return result;
```

but the file contains:

```text
return result;
```

three times.

Edit doesn't know which one you mean.

Therefore, it fails.

### Why?

This is a **safety mechanism**.

It prevents Claude from accidentally modifying the wrong location.

---

## 11. Current Claude Code: Widen the Anchor

In current Claude Code, the better real-world response is:

### Step 1

Make `old_string` more specific.

Instead of:

```text
return result;
```

use surrounding context:

```text
if (isValid) {
    return result;
}
```

Now the match may be unique.

### Step 2

If you genuinely want every occurrence changed:

```text
replace_all: true
```

### Step 3

If you still cannot uniquely identify the target:

> Fall back to **Read + Write**.

---

## 12. ⚠️ Exam vs Real-World Behaviour

This distinction is explicitly called out by the guide.

### Current Claude Code

After a non-unique Edit match:

```text
1. Widen old_string
       OR
2. use replace_all
       ↓
3. Read + Write only if necessary
```

### Exam answer

The exam guide expects:

```text
Edit
  ↓
Edit fails
  ↓
Read + Write
```

So if the **exam question specifically asks for the documented exam fallback**, answer:

> **Read + Write**

But in real Claude Code usage:

> **Widen the anchor first or use `replace_all` when appropriate.**

---

## 13. Why Not Read + Write First?

Suppose you only need to change one line.

### Bad approach

```text
Read entire file
      ↓
Modify entire file
      ↓
Write entire file
```

This consumes the context for the whole file.

### Better

```text
Edit
  ↓
Change one specific piece
```

Therefore:

> **Don't default to Read + Write for every modification.**

---

## 14. Incremental Codebase Understanding

🔥 This is another major exam concept.

When exploring a large codebase:

### ❌ Wrong approach

```text
Read every file
      ↓
Understand everything
      ↓
Start solving
```

If you have 200 files, you could consume enormous amounts of context on irrelevant files.

### ✅ Correct approach

```text
Discover
   ↓
Read relevant files
   ↓
Discover more dependencies
   ↓
Read only what matters
```

This is called:

> **Incremental discovery**

---

## 15. The Correct Incremental Sequence

Memorise this sequence:

```text
Grep
  ↓
Read
  ↓
Grep again
  ↓
Read only what is needed
```

### Step 1 — Grep

Find the initial entry point.

Example:

```text
Grep: "processOrder"
```

### Step 2 — Read

Read the relevant file.

Understand:

- imports
- wrappers
- exported names
- data flow

### Step 3 — Grep again

You may discover that the function is exposed under another name.

Search for the new name.

### Step 4 — Read only relevant files

Don't load the entire repository.

---

## 16. Why Read Everything Upfront Is Bad

Suppose the repository contains:

```text
200 files
```

Your task concerns only:

```text
8 files
```

Reading everything means:

```text
200 files → context
```

when you actually need:

```text
8 files → context
```

### Result

Reading everything:

- wastes context
- wastes tokens
- reduces useful context capacity
- makes the investigation less efficient

### Exam rule

> **Start narrow and expand only when evidence tells you to.**

---

## 17. Tracing Function Usage Through Wrappers

🔥 This is a subtle but important exam scenario.

Suppose:

```text
orders.ts
```

contains:

```text
processOrder()
```

But another file does:

```text
export { processOrder as submitOrder }
```

Now some consumers use:

```text
submitOrder()
```

instead of:

```text
processOrder()
```

---

## 18. Why One Grep Is Not Enough

If you search:

```text
Grep: "processOrder"
```

you may find:

```text
orders.ts
```

and some direct callers.

But you may **miss consumers using `submitOrder`**.

Why?

Because their files contain:

```text
submitOrder
```

not:

```text
processOrder
```

---

## 19. Correct Wrapper-Tracing Process

Use:

```text
1. Grep processOrder
        ↓
2. Read defining file
        ↓
3. Discover exported name: submitOrder
        ↓
4. Grep submitOrder
        ↓
5. Find indirect consumers
```

### Memory

> **Grep → Read → discover rename → Grep again**

---

## 20. Barrel Files

A barrel file is something like:

```text
utils/index.ts
```

that re-exports functionality:

```text
export { processOrder as submitOrder }
```

Consumers might import:

```text
import { submitOrder } from "utils";
```

They never mention:

```text
processOrder
```

directly.

Therefore, you may also need to:

```text
Grep for the barrel module
```

to discover consumers.

---

## 21. Deprecation Scenario — ⭐ Very Important

The guide gives a classic exam scenario:

> Find every file that calls a deprecated function AND find the test files that exercise those callers.

Suppose the deprecated function is:

```text
processLegacyOrder()
```

### Correct sequence:

```text
Grep
  ↓
Find direct callers
  ↓
Glob
  ↓
Find corresponding test files
  ↓
Grep again
  ↓
Find wrapper-based callers
```

### Memory:

> **Grep → Glob → Grep**

---

## 22. Why Grep First?

Start with:

```text
Grep: "processLegacyOrder"
```

because you need to find:

> **Which files contain calls to the deprecated function?**

That's a **content search**.

---

## 23. Why Glob Second?

Suppose Grep finds:

```text
src/OrderProcessor.ts
src/RefundHandler.ts
```

Now you want their sibling tests:

```text
src/OrderProcessor.test.tsx
src/RefundHandler.test.tsx
```

This is a **path/naming problem**.

Therefore:

```text
Glob
```

is the correct tool.

---

## 24. Why Grep Again?

Suppose:

```text
RefundHandler.ts
```

contains:

```text
applyLegacyOrder()
```

and internally:

```text
applyLegacyOrder()
    ↓
processLegacyOrder()
```

Tests may call:

```text
applyLegacyOrder()
```

without mentioning:

```text
processLegacyOrder()
```

So you need:

```text
Grep: "applyLegacyOrder"
```

to find indirect coverage.

### Therefore:

```text
Grep
 ↓
Glob
 ↓
Grep
```

---

## 25. Complete Deprecation Example

Suppose:

```text
processLegacyOrder()
```

is deprecated.

### Step 1

```text
Grep: "processLegacyOrder"
```

Find:

```text
OrderProcessor.ts
RefundHandler.ts
```

### Step 2

```text
Glob: "**/OrderProcessor.test.*"
Glob: "**/RefundHandler.test.*"
```

Find:

```text
OrderProcessor.test.tsx
RefundHandler.test.tsx
```

### Step 3

Read caller files.

Discover:

```text
RefundHandler.ts
    ↓
applyLegacyOrder()
    ↓
processLegacyOrder()
```

### Step 4

```text
Grep: "applyLegacyOrder"
```

Find additional consumers/tests.

### Result

You discover:

- direct callers
- sibling tests
- indirect callers
- indirect test coverage

---

## 26. Built-in Tool Decision Tree

Use this in the exam:

```text
What are you trying to do?
        │
        ├── Search INSIDE files
        │       ↓
        │     Grep
        │
        ├── Find files by name/path
        │       ↓
        │     Glob
        │
        ├── Read/understand a file
        │       ↓
        │     Read
        │
        ├── Modify specific text
        │       ↓
        │     Edit
        │
        ├── Create/replace complete file
        │       ↓
        │     Write
        │
        └── Run shell command
                ↓
              Bash
```

---

## 27. The Six Tools — Easy Memory

| Tool | Think |
|---|---|
| **Read** | 👀 Read |
| **Write** | 📝 Whole file |
| **Edit** | ✏️ Specific change |
| **Bash** | 💻 Shell |
| **Grep** | 🔎 Inside content |
| **Glob** | 📁 File paths |

### One-line memory

> **Read → understand, Edit → change part, Write → replace whole, Bash → execute, Grep → search content, Glob → search paths.**

---

## 28. Exam Traps

### Trap 1 — Glob for function callers

❌ Wrong.

Function callers are inside file contents.

✅ **Grep**

### Trap 2 — Grep for test files

❌ Wrong tool.

Finding:

```text
**/*.test.tsx
```

is a path-pattern problem.

✅ **Glob**

### Trap 3 — Read every file first

❌ Wrong.

It wastes context.

✅ **Grep → Read → expand incrementally**

### Trap 4 — Read + Write for every modification

❌ Wrong.

Use:

✅ **Edit first**

### Trap 5 — Edit with ambiguous text

If the same text occurs multiple times, Edit cannot safely choose.

### Exam answer:

✅ **Read + Write**

### Current Claude Code:

✅ Widen `old_string` or use `replace_all` where appropriate; Read + Write is the fallback.

### Trap 6 — Grep only once

❌ Wrong when wrappers/re-exports exist.

You may miss indirect consumers.

✅ **Grep → Read → Grep again**

### Trap 7 — Glob first for deprecated function

❌ Wrong.

First find **where the function is referenced**.

✅ Grep first.

---

## 29. Practice Scenario

### Question

A developer needs to:

> Find all files that call the deprecated `processLegacyOrder()` and also find the test files for those callers.

What is the correct sequence?

### Correct answer:

> **Grep → Glob → Grep**

Specifically:

```text
1. Grep "processLegacyOrder"
       ↓
2. Glob for corresponding test files
       ↓
3. Grep for wrapper names discovered during inspection
```

### Why?

```text
Grep
→ content search

Glob
→ test-file path matching

Grep again
→ indirect/wrapper usage
```

**Correct answer: Option C.**

---

## 30. Build Exercise — What You Should Understand

The lesson's build exercise asks you to trace and refactor a deprecated function.

### Step 1 — Find callers

Use:

```text
Grep
```

Example:

```text
Grep: "processLegacyOrder"
```

### Step 2 — Find tests

Use:

```text
Glob
```

Example:

```text
**/*.test.tsx
```

or the caller-specific test naming pattern.

### Step 3 — Understand callers

Use:

```text
Read
```

Only read the files discovered by the previous steps.

### Step 4 — Replace deprecated calls

Use:

```text
Edit
```

Example:

```text
processLegacyOrder(orderId)
```

becomes:

```text
processOrder(orderId, { validate: true })
```

### Step 5 — Handle Edit failure

If Edit reports:

```text
old_string matches 3 locations
```

### Real-world current Claude Code:

```text
Widen old_string
       OR
replace_all: true
```

### Exam:

```text
Read + Write
```

---

## 31. One-Minute Revision

### Content vs Path

```text
Grep → CONTENT
Glob → PATH
```

### File operations

```text
Read  → inspect
Edit  → targeted modification
Write → whole-file replacement
```

### Exploration

```text
Grep
 ↓
Read
 ↓
Grep again
 ↓
Read only what matters
```

### Deprecated-function investigation

```text
Grep
 ↓
Glob
 ↓
Grep
```

### Modification

```text
Edit first
 ↓
If ambiguous:
   Real world → widen anchor / replace_all
   Exam → Read + Write
```

---

## 32. Golden Rules ⭐

### Rule 1

> **Grep searches file contents.**

### Rule 2

> **Glob matches file paths/names.**

### Rule 3

> **Function callers → Grep.**

### Rule 4

> **Test files by naming pattern → Glob.**

### Rule 5

> **Read only after you've identified relevant files.**

### Rule 6

> **Don't read the entire codebase upfront.**

### Rule 7

> **Edit is the preferred tool for targeted modifications.**

### Rule 8

> **Read + Write is the exam fallback when Edit cannot identify a unique target.**

### Rule 9

> **Current Claude Code can often recover from ambiguous Edit matches by widening `old_string` or using `replace_all`.**

### Rule 10

> **Wrappers and barrel files require additional Grep searches.**

### Rule 11

> **Deprecated-function investigation commonly follows Grep → Glob → Grep.**

---

## 33. What to Memorise for the Exam

If you remember only **10 things**, remember these:

1. **Grep = search file contents.**
2. **Glob = search file paths/names.**
3. **Function callers → Grep.**
4. **Files matching `*.test.*` → Glob.**
5. **Read → understand relevant files.**
6. **Edit → targeted modification.**
7. **Write → create/replace an entire file.**
8. **Never read the entire codebase upfront; use incremental discovery.**
9. **Wrapper/re-export → Read the definition, then Grep the new exported name.**
10. **Deprecated-function task → Grep → Glob → Grep.**

### 🧠 Final memory sentence

> **Grep finds what is inside files, Glob finds the files themselves, Read helps you understand discovered code, Edit makes precise changes, Write handles whole-file replacement, and good codebase exploration always starts narrow and expands only when the evidence requires it.**
