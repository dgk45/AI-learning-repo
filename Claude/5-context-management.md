# Domain 5 — Task 5.1: Context Window Management

Here are **simple, exam-focused notes** for the page you shared. I’ll keep the same structure and level of detail as your Domain 1 and Domain 2 notes.

## 1. What You Need to Know

Context window management is a foundation of reliable Claude systems.

The main goal is:

> **Summarise narrative; persist critical facts.**

A long conversation can become unreliable even when it has not technically reached the context limit. The model can lose its grip on exact details as verbose history accumulates.

The most important exam ideas are:

- Use a **persistent case facts block** for critical facts.
- Never let critical facts disappear into a summary.
- Keep key findings near the beginning of the prompt.
- Use explicit section headers for detailed outputs.
- Trim verbose tool results before they enter conversation history.
- Remember that the **Claude API is stateless**.
- Upstream agents should return structured data rather than verbose reasoning.
- Use prompt caching when stable context is repeatedly reused.

## 2. The Progressive Summarisation Trap

A common approach is to repeatedly summarise older conversation history.

This saves tokens, but it can destroy information that is essential for later decisions.

### Information that can be lost

Progressive summarisation can accidentally remove:

- Numerical values
- Dates
- Percentages
- Customer-stated expectations
- Order IDs
- Statuses
- Other exact case facts

### Why this is dangerous

A summary may preserve the general story while losing the exact facts needed to make a correct decision.

For example:

> Original conversation: “Customer expected delivery on 12 March, order `ORD-4821`, amount £249.99.”

A summary might become:

> “Customer has a delivery complaint.”

The narrative remains, but the critical facts have disappeared.

### Correct solution: Persistent Case Facts Block

Maintain a structured block containing critical facts.

Include this block in **every prompt**, outside the summarised conversation history.

The block should **never be summarised**.

Memory trick:

> **Narrative can be compressed. Facts must persist.**

## 3. Persistent Case Facts

A persistent case facts block prevents important information from being lost during long conversations.

For example:

```text
CASE FACTS
- Order ID: ORD-4821
- Order amount: £249.99
- Expected delivery: 12 March
- Current status: Delayed
- Customer requested: Refund
```

The conversation history can be summarised, but this structured block remains intact.

### Why this works

Instead of relying on the model to remember important details from a long history:

```text
Critical facts
      ↓
Persistent structured block
      ↓
Included in every request
```

This gives the model reliable access to the information that must not disappear.

## 4. Multi-Issue Sessions and Cross-Contamination

Long customer sessions may contain multiple issues.

For example:

- Order A has a delivery problem.
- Order B has a refund issue.
- Order C has a damaged-product complaint.

If these are stored only in narrative history, details can become mixed.

### Correct approach

Create separate structured issue entries.

```text
ISSUE 1
- Order ID: ORD-1001
- Amount: £100
- Status: Delayed

ISSUE 2
- Order ID: ORD-1002
- Amount: £250
- Status: Refund requested
```

This reduces **cross-contamination** between unrelated issues.

### Exam point

If a session contains several issues:

> **Separate the issues structurally rather than relying on conversational memory.**

## 5. The “Lost in the Middle” Problem

Important information can become harder for the model to use when it is buried in the middle of a large context.

### Better prompt organisation

Put important findings near the beginning.

For detailed outputs:

- Use explicit section headers.
- Group related information.
- Make important findings easy to locate.

A useful structure is:

```text
KEY FINDINGS
...

DETAILED ANALYSIS
...

SUPPORTING INFORMATION
...
```

### Memory trick

> **Important first. Details organised.**

The problem is not only the number of tokens. The location and organisation of information inside the context also matter.

## 6. Tool Result Trimming

Tools can return very verbose outputs.

If the complete tool result is placed into conversation history, it consumes context and can reduce the quality of later reasoning.

### Correct pattern

```text
Tool
 ↓
Verbose result
 ↓
Trim / extract relevant information
 ↓
Conversation history
```

Only the information needed for later reasoning should enter the context.

### Where should trimming happen?

Ideally:

- In a `PostToolUse` hook, or
- Inside the tool implementation itself.

This prevents unnecessary tool output from accumulating in the conversation.

### Exam trap

Do not assume that because a tool result is available, the entire raw result should always be preserved in the model context.

> **Trim verbose tool output before it becomes history.**

## 7. Claude API Is Stateless

The Claude API is **stateless**.

This means each request does not automatically remember the previous request.

If the next request needs earlier information, that information must be included again.

Conceptually:

```text
Request 1
   ↓
Response 1

Request 2
   ↓
Must include the history/facts required for coherence
```

### Exam point

Do not assume that the API automatically retains the complete conversation between independent requests.

The application is responsible for providing the context needed for the next request.

## 8. Structured Outputs from Upstream Agents

In multi-agent systems, upstream agents should avoid returning huge amounts of verbose reasoning to downstream agents.

Instead, return structured information such as:

- Key facts
- Citations
- Relevance scores
- Dates
- Source locations
- Methodological context

Example:

```text
Finding:
Customer was charged twice.

Source:
billing_record_2026_03_12

Date:
12 March 2026

Relevance:
High

Evidence location:
Transaction rows 14–15
```

### Why?

The downstream agent receives the information it actually needs without wasting context on unnecessary narrative.

Memory trick:

> **Upstream agents should pass useful structured evidence, not context-heavy storytelling.**

## 9. Prompt Caching

Prompt caching is useful when the same stable context is repeatedly supplied.

The important exam-level idea is to separate:

- **Stable content**
- **Dynamic content**

A typical pattern is:

```text
Stable / cacheable prefix
        ↓
Dynamic content
```

Use `cache_control` for the cacheable content.

The guide emphasises:

- Put static content first.
- Put dynamic content after it.
- Use the top-level `system` field for system instructions.
- Use `messages` for user/assistant conversation content.

### Exam scope

The guide states that detailed implementation mechanics are beyond what you need to memorise for the exam.

Focus on:

> **Prompt caching reduces repeated processing of stable context.**

## Exam Traps

### Trap 1 — “Summarise everything to save tokens.”

Wrong.

Repeated summarisation can destroy exact facts such as:

- Dates
- Amounts
- Percentages
- IDs
- Customer expectations
- Statuses

Correct concept:

> **Summarise narrative, but maintain a persistent case facts block for critical information.**

---

### Trap 2 — “A larger context window solves context degradation.”

Wrong.

A larger context window does not automatically solve the problem of attention and information retrieval quality.

Correct concept:

> Organise context carefully and preserve critical facts explicitly.

---

### Trap 3 — “The model will remember the API conversation automatically.”

Wrong.

The Claude API is stateless.

Correct concept:

> Each request must contain the conversation history or structured facts needed for coherence.

---

### Trap 4 — “Keep every tool result in full.”

Wrong.

Verbose tool results can consume context unnecessarily.

Correct concept:

> Trim tool results before they enter conversation history, preferably in `PostToolUse` or the tool implementation.

---

### Trap 5 — “Put important findings anywhere in the prompt.”

Wrong.

Important findings can become harder to use when buried in a large context.

Correct concept:

> Put key findings near the beginning and use explicit section headers.

## Practice Scenario

### Scenario

A customer-support agent handles a 20-turn conversation involving three different orders. The application periodically summarises the old conversation to reduce context usage.

The customer later asks for a refund for a specific order.

### Question

What is the safest way to preserve the information needed for the refund decision?

### Correct answer

Maintain a **persistent case facts block** containing structured information for each issue/order and include it in every request outside the summarised conversation history.

### Why?

The summary may accidentally remove:

- Order IDs
- Amounts
- Statuses
- Dates
- Customer expectations

A persistent structured block prevents those critical facts from being lost and reduces cross-contamination between multiple issues.

## Build Exercise

Build a context-management workflow with these steps:

1. Create a **case facts extractor**.
2. Extract the critical facts from the conversation.
3. Store them in a persistent structured block.
4. Keep the block outside summarised history.
5. For a multi-issue session, create separate structured issue entries.
6. Create a tool-result trimming step.
7. Trim a verbose tool response containing 40+ fields down to the 5 fields actually relevant to the task.
8. Run a conversation test across **6–8 turns**.
9. Verify that critical facts remain available after summarisation.
10. Put key findings at the top of detailed outputs.
11. Use explicit section headers.
12. Verify that irrelevant tool output is not unnecessarily carried forward.

## Decision Framework

### When managing a long conversation

```text
Is the information critical to future decisions?
        ↓
      Yes
        ↓
Put it in the persistent case facts block
        ↓
Never summarise it
```

```text
Is it narrative history?
        ↓
      Yes
        ↓
Can it be safely summarised?
        ↓
      Yes
        ↓
Summarise it
```

```text
Did a tool return a very verbose result?
        ↓
      Yes
        ↓
Trim it before adding it to history
        ↓
Keep only relevant information
```

### For multiple issues

```text
Multiple orders/issues?
        ↓
      Yes
        ↓
Create separate structured issue entries
        ↓
Prevent cross-contamination
```

### For repeated stable context

```text
Stable context reused repeatedly?
        ↓
      Yes
        ↓
Consider prompt caching
        ↓
Static content first
        ↓
Dynamic content after it
```

## Golden Rules ⭐

1. **Summarise narrative; persist critical facts.**
2. Use a **persistent case facts block** for information that must not be lost.
3. Never summarise critical facts such as IDs, dates, amounts, statuses, and customer expectations.
4. Multiple issues should be represented as separate structured entries.
5. Key findings should appear near the beginning.
6. Use explicit section headers for detailed outputs.
7. Trim verbose tool results before they enter conversation history.
8. `PostToolUse` hooks or tool implementations are good places to perform trimming.
9. The Claude API is stateless.
10. Every request must contain the context required for coherence.
11. Upstream agents should return structured facts, citations, dates, relevance, source locations, and methodological context.
12. Prompt caching is useful for stable repeated context.
13. Static/cacheable content should come before dynamic content.
14. **Context management is about information quality and attention, not only token count.**

## What to Memorise for the Exam

### 1. Persistent Case Facts

```text
Critical facts
→ Persistent structured block
→ Included in every prompt
→ Never summarised
```

### 2. Information that must survive summarisation

```text
IDs
Dates
Amounts
Percentages
Statuses
Customer expectations
```

### 3. Multiple issues

```text
One issue = one structured entry
```

This prevents cross-contamination.

### 4. Tool results

```text
Verbose tool result
→ Trim
→ Keep relevant fields
→ Add to context
```

### 5. Claude API

```text
Claude API = Stateless
```

The application must provide the required context in each request.

### 6. Upstream agents

Prefer:

```text
Key facts
Citations
Relevance
Dates
Source locations
Methodological context
```

over large amounts of verbose reasoning.

### 7. Prompt caching

Remember:

```text
Stable content → cache
Dynamic content → after stable content
```

### Final memory

> **Summarise the narrative but persist critical facts in a structured case block; keep key findings visible, trim verbose tool results, remember the Claude API is stateless, and use structured upstream outputs and caching to protect context quality.**

---

# Domain 5 — Task 5.2: Escalation & Ambiguity Resolution

Here are **simple, exam-focused notes** for the page you shared. I’ll keep the same structure and level of detail as your Domain 1 and Domain 2 notes.

## 1. What You Need to Know

The lesson focuses on deciding **when Claude should continue handling a request and when it should escalate to a human**.

The exam expects a very specific set of escalation triggers.

There are exactly **3 valid escalation triggers**:

1. The customer explicitly requests a human.
2. There is a policy exception or policy gap.
3. The agent cannot make meaningful progress after a genuine attempt.

There are also **2 unreliable escalation triggers**:

1. Customer sentiment.
2. Self-reported confidence scores.

The most important memory rule is:

> **3 valid triggers + 2 unreliable triggers.**

## 2. The Three Valid Escalation Triggers

### Trigger 1 — Customer Explicitly Requests a Human

If the customer explicitly asks for a human:

> **Escalate immediately.**

There are **no exceptions**.

Do not:

- Investigate first.
- Ask unnecessary questions.
- Try to solve the problem first.
- Delay escalation.

### Example

Customer:

> “I want to speak to a human.”

Correct behaviour:

```text
Immediate escalation
```

### Exam trap

A tempting answer is:

> “Try to resolve the issue first, then escalate if necessary.”

Wrong.

An explicit human request requires immediate escalation.

---

### Trigger 2 — Policy Exception or Policy Gap

Escalate when the organisation's policy does not clearly cover the situation.

This is a **policy gap**.

### Important distinction

| Situation | Meaning | Escalate? |
|---|---|---|
| Policy gap | Policy is silent / does not cover the case | Yes |
| Policy violation | Policy explicitly says no | No, follow the documented policy |

This distinction is highly important for the exam.

### Policy gap

Example:

> The policy describes refunds for damaged products but says nothing about a particular unusual scenario.

This is a gap.

Correct:

> Escalate for human/policy decision.

### Policy violation

Example:

> The policy explicitly states that the requested action is not allowed.

This is not automatically an escalation.

Correct:

> Follow the documented policy.

Memory trick:

> **Gap = unclear → escalate.**  
> **Violation = documented no → follow policy.**

---

### Trigger 3 — Genuine Failure to Make Meaningful Progress

Escalate when the agent has made a genuine attempt but cannot make meaningful progress.

This is different from simply being uncertain.

The agent should first make a genuine attempt when appropriate.

If meaningful progress remains impossible:

```text
Genuine attempt
      ↓
No meaningful progress
      ↓
Escalate
```

## 3. Two Unreliable Escalation Triggers

### Unreliable Trigger 1 — Sentiment

Do not automatically escalate because the customer is angry, frustrated, or upset.

### Example

Customer:

> “This is extremely frustrating. Where is my order?”

If the problem is straightforward and the agent can resolve it:

> Acknowledge the frustration and resolve the issue.

Do not escalate simply because the customer is frustrated.

### Important nuance

If the customer explicitly asks for a human after the agent offers help:

> Escalate.

The trigger is the **human request**, not the sentiment.

---

### Unreliable Trigger 2 — Self-Reported Confidence

Do not use Claude's own confidence score as the primary escalation criterion.

A model saying:

> “I am 95% confident.”

does not guarantee that the answer is actually 95% accurate.

Correct approach:

> Use explicit escalation criteria rather than relying on self-reported confidence.

## 4. Handling Customer Frustration

The exam distinguishes several cases.

| Situation | Correct action |
|---|---|
| Customer is frustrated but issue is straightforward | Acknowledge + resolve |
| Customer explicitly requests a human at the start | Immediate escalation |
| Customer requests a human after the agent offers help | Escalate |
| Customer is merely emotional/frustrated | Do not escalate automatically |

### Memory rule

> **Frustration ≠ escalation. Human request = escalation.**

## 5. Ambiguous Customer Matching

Another important problem is identifying the correct customer/order when multiple records match.

### Example

A customer provides a name.

The system finds:

```text
Customer A — same name
Customer B — same name
Customer C — same name
```

Do **not** choose:

- The most recent record.
- The most active record.
- The first record.
- The record that “looks most likely”.

### Correct behaviour

Ask for an additional identifier.

Useful identifiers include:

- Email address
- Phone number
- Order number

Example:

> “Could you provide your order number or the email address associated with the account?”

### Why?

Guessing can:

- Select the wrong customer.
- Expose private information.
- Cause incorrect actions.
- Create a privacy/security risk.

Memory trick:

> **Multiple matches → ask, never guess.**

## 6. Best First Calibration Approach

Suppose you are building an escalation classifier.

What should you do first?

Do **not** immediately build complex:

- Sentiment infrastructure
- Confidence scoring
- Classifier pipelines

Start with:

> **Explicit escalation criteria + few-shot examples in the system prompt.**

### Why?

The model needs clear rules describing exactly:

- What must be escalated.
- What must not be escalated.
- How to handle ambiguous cases.

Few-shot examples then demonstrate how those rules should be applied.

## Exam Traps

### Trap 1 — “Frustrated customers should always be escalated.”

Wrong.

Sentiment is not a reliable escalation trigger.

Correct concept:

> Frustration alone does not require escalation.

---

### Trap 2 — “A confidence score above 90% means no escalation.”

Wrong.

Self-reported confidence is not reliably calibrated.

Correct concept:

> Use explicit escalation criteria.

---

### Trap 3 — “If the policy does not allow something, escalate.”

Not necessarily.

Distinguish:

```text
Policy gap
→ Policy is silent
→ Escalate

Policy violation
→ Policy explicitly prohibits it
→ Follow policy
```

---

### Trap 4 — “Pick the most recent matching customer.”

Wrong.

When multiple records match, guessing creates privacy and correctness risks.

Correct concept:

> Ask for another identifier.

---

### Trap 5 — “Investigate before escalating an explicit human request.”

Wrong.

If the customer explicitly asks for a human:

> Escalate immediately.

## Practice Scenario

### Scenario

A customer says:

> “I am very frustrated. My order is late. Can you tell me when it will arrive?”

The system can retrieve the order successfully.

### Question

Should the case be escalated because the customer is frustrated?

### Correct answer

No.

### Why?

Sentiment alone is not a valid escalation trigger.

The agent should acknowledge the frustration and resolve the straightforward issue.

---

### Scenario 2

A customer says:

> “I want to speak to a human.”

### Correct answer

Escalate immediately.

### Why?

The customer explicitly requested a human.

---

### Scenario 3

The system finds three customers with the same name.

### Correct answer

Ask for an additional identifier such as:

```text
Email
Phone number
Order number
```

### Why?

Never guess which customer is correct.

## Build Exercise

Build an escalation and ambiguity-resolution workflow:

1. Define the **3 explicit escalation criteria**.
2. Add few-shot examples to the system prompt.
3. Include a scenario for an explicit human request.
4. Include a policy-gap scenario.
5. Include a policy-violation scenario.
6. Include a genuine-no-progress scenario.
7. Include a frustrated-but-straightforward scenario.
8. Add an ambiguous customer-matching scenario.
9. Require an additional identifier when multiple records match.
10. Test all 4 major scenarios.
11. Verify that explicit human requests always escalate.
12. Verify that sentiment alone does not escalate.
13. Verify that confidence scores do not replace explicit criteria.

## Decision Framework

```text
Did the customer explicitly request a human?
        ↓
      Yes
        ↓
Immediate escalation
```

```text
Is there a policy gap?
        ↓
      Yes
        ↓
Escalate
```

```text
Is this a policy violation?
        ↓
      Yes
        ↓
Follow documented policy
```

```text
Was there a genuine attempt?
        ↓
      Yes
        ↓
Can meaningful progress still be made?
        ↓
     No
        ↓
Escalate
```

### Customer sentiment

```text
Frustrated?
   ↓
Can the issue be resolved?
   ↓
Yes → Acknowledge + resolve
```

### Customer matching

```text
One matching record?
        ↓
      Yes
        ↓
Use it

Multiple matching records?
        ↓
      Yes
        ↓
Ask for another identifier
        ↓
Never guess
```

## Golden Rules ⭐

1. There are exactly **3 valid escalation triggers**.
2. Explicit human request → **immediate escalation**.
3. Policy gap → escalate.
4. Policy violation → follow documented policy.
5. Genuine inability to make meaningful progress → escalate.
6. Sentiment alone is not a reliable escalation trigger.
7. Self-reported confidence is not a reliable escalation trigger.
8. Frustration does not automatically mean escalation.
9. Multiple customer matches → ask for another identifier.
10. Never guess which customer record is correct.
11. Start calibration with explicit criteria and few-shot examples.
12. **Human request beats all other considerations.**

## What to Memorise for the Exam

### The 3 valid triggers

```text
1. Explicit human request
2. Policy gap / exception
3. No meaningful progress after genuine attempt
```

### The 2 unreliable triggers

```text
1. Sentiment
2. Self-reported confidence
```

### Matching rule

```text
Multiple matches
→ Ask for identifier
→ Never guess
```

### Policy distinction

```text
Gap
→ Escalate

Violation
→ Follow policy
```

### Final memory

> **Escalate for an explicit human request, a genuine policy gap, or inability to make meaningful progress; do not escalate merely for sentiment or confidence, and when records are ambiguous, ask for an identifier instead of guessing.**

---

# Domain 5 — Task 5.3: Error Propagation in Multi-Agent Systems

Here are **simple, exam-focused notes** for the page you shared. I’ll keep the same structure and level of detail as your Domain 1 and Domain 2 notes.

## 1. What You Need to Know

In a multi-agent system, one subagent can fail while the overall workflow still has useful work to do.

The key principle is:

> **Do not hide failures and do not unnecessarily terminate the entire workflow.**

Instead, propagate a **structured error context** so the coordinator can decide what to do next.

Every propagated failure should contain:

1. Failure type
2. Attempted action
3. Partial results
4. Potential alternative approaches

Memory trick:

> **Type + Attempt + Partial + Alternative**

## 2. Structured Error Context

When a subagent fails, downstream agents need enough information to understand the failure.

A useful structure is:

```text
Failure type:
Transient

Attempted action:
Query order database

Partial results:
No complete response received

Potential alternatives:
Retry query / use secondary source
```

### Required information

| Field | Purpose |
|---|---|
| Failure type | Explains what kind of failure occurred |
| Attempted action | Shows what the agent tried |
| Partial results | Preserves useful information already obtained |
| Potential alternatives | Helps the coordinator recover |

## 3. Four Important Failure Types

### 1. Transient failure

A temporary technical problem.

Examples:

- Timeout
- Rate limit

Typical response:

> Retry or use another recovery strategy.

---

### 2. Validation failure

The input or query is invalid.

Examples:

- Bad input
- Invalid query

Typical response:

> Correct the input/query before trying again.

---

### 3. Business failure

A business rule prevents the requested action.

Example:

> A requested transaction violates a business rule.

Typical response:

> Do not blindly retry. The coordinator should handle the business constraint.

---

### 4. Permission failure

The agent does not have access.

Example:

> Access denied to a required system.

Typical response:

> Consider alternative access, another source, or escalation.

## 4. Two Catastrophic Anti-Patterns

There are two particularly dangerous ways to handle subagent failures.

### Anti-pattern 1 — Silent Suppression

Example:

A database query times out.

The subagent returns:

```json
{
  "results": [],
  "status": "success"
}
```

This is dangerous.

The empty result could be interpreted as:

> “The database was checked and there were no results.”

But the database was never successfully queried.

### Correct approach

Return the failure explicitly:

```text
status: failure
failure_type: transient
attempted_action: database query
partial_results: none
```

Memory:

> **Failure ≠ empty result.**

---

### Anti-pattern 2 — Workflow Termination

One subagent fails, so the entire multi-agent workflow is stopped.

This is often unnecessary.

For example:

```text
Agent A → Success
Agent B → Success
Agent C → Timeout
Agent D → Could still provide useful information
```

Killing the entire workflow loses useful results.

### Correct approach

Propagate the structured error to the coordinator.

The coordinator can then:

- Recover locally.
- Retry.
- Use partial results.
- Continue unaffected branches.
- Escalate if necessary.

## 5. Access Failure vs Valid Empty Result

This is one of the most important exam distinctions.

### Access failure

The query did not successfully execute.

Examples:

- Timeout
- Connection failure
- Permission denied

Meaning:

> We do not know whether matching records exist.

Therefore:

> Retry may be appropriate.

---

### Valid empty result

The query executed successfully but returned no matches.

Meaning:

> There genuinely were no matching records.

Therefore:

> This is the answer. Do not retry simply because the result is empty.

### Comparison

| Situation | Meaning | Retry? |
|---|---|---|
| Timeout | Query did not complete | Consider retry |
| Connection failure | Query did not execute | Consider retry |
| Permission denied | Access failed | Recovery/escalation |
| Successful query + zero matches | Valid empty result | No retry |

Memory trick:

> **Access failure ≠ valid empty.**

## 6. Coverage Annotations

Suppose a synthesis agent is producing a report from several sources.

One source fails.

The final report should not silently omit the affected topic.

Instead, the synthesis should say that coverage is limited.

Example:

```text
Order-history analysis:
Limited because the order-history source was unavailable.
```

This is called a **coverage annotation**.

### Why?

The consumer needs to know:

> “Was there no information, or did the system fail to obtain the information?”

Without the annotation, a missing section can be mistaken for a negative finding.

## 7. Local Recovery Before Propagation

A subagent should attempt reasonable local recovery for transient failures before propagating the error.

The guide's build exercise specifies:

```text
Retry 1 → 1 second
Retry 2 → 2 seconds
Retry 3 → 4 seconds
```

This is **exponential backoff**.

Conceptually:

```text
Failure
  ↓
Retry
  ↓
Failure
  ↓
Wait longer
  ↓
Retry
```

After reasonable recovery attempts fail:

> Propagate the structured error to the coordinator.

## 8. Coordinator's Role

The coordinator receives:

```text
Failure type
Attempted action
Partial results
Potential alternatives
```

Then it decides what to do.

Possible actions include:

- Targeted recovery
- Retry
- Use partial results
- Continue other workflow branches
- Find an alternative approach
- Escalate

The coordinator should not treat every failure identically.

### Example

```text
Transient timeout
→ Retry

Valid empty result
→ Accept as answer

Permission failure
→ Alternative access / escalation

Business-rule failure
→ Apply business logic
```

## Exam Traps

### Trap 1 — “An empty result means there are no results.”

Not always.

If the underlying query timed out, an empty result may actually represent a failure.

Correct concept:

> Distinguish **access failure** from **valid empty result**.

---

### Trap 2 — “Return success with an empty list after a timeout.”

Wrong.

This is **silent suppression**.

Correct concept:

> Propagate the failure explicitly.

---

### Trap 3 — “Any subagent failure should terminate the workflow.”

Wrong.

A single failed branch does not necessarily invalidate all other results.

Correct concept:

> Propagate structured errors and let the coordinator decide.

---

### Trap 4 — “Retry every failure.”

Wrong.

Not all failures are transient.

For example:

- Business-rule failure
- Permission failure
- Valid empty result

may require different handling.

Correct concept:

> Classify the failure before choosing recovery.

---

### Trap 5 — “Silently omit a failed source from the final report.”

Wrong.

This hides incomplete coverage.

Correct concept:

> Add a coverage annotation describing the unavailable source/topic.

## Practice Scenario

### Scenario

A research subagent queries a database but receives a timeout.

It returns:

```json
{
  "results": [],
  "status": "success"
}
```

### Question

What is wrong with this response?

### Correct answer

The timeout has been silently converted into an apparently successful empty result.

### Why?

The coordinator may interpret the response as:

> “The database was queried and there were no records.”

But the query never successfully completed.

The failure should be propagated with structured error information.

---

### Scenario 2

A database query executes successfully and returns zero records.

### Correct answer

Treat the empty result as a valid answer.

### Why?

The query actually executed.

There is no reason to retry simply because the result is empty.

---

### Scenario 3

One research subagent fails, but three other agents successfully return useful information.

### Correct answer

Do not terminate the entire workflow.

### Why?

The coordinator can preserve the successful results, recover the failed branch if possible, and add a coverage annotation if the missing information affects the final synthesis.

## Build Exercise

Build a structured multi-agent error-handling workflow:

1. Define an error schema.
2. Include:
   - Failure type
   - Attempted action
   - Partial results
   - Potential alternatives
3. Support transient failures.
4. Support validation failures.
5. Support business failures.
6. Support permission failures.
7. Create a test for access failure.
8. Create a test for a valid empty result.
9. Verify that access failure is not represented as a successful empty result.
10. Add local retry logic for transient failures.
11. Use exponential backoff:
    - 1 second
    - 2 seconds
    - 4 seconds
12. Propagate unrecovered failures to the coordinator.
13. Allow the coordinator to continue unaffected branches.
14. Add coverage annotations when source failure limits the final synthesis.

## Decision Framework

```text
Did the operation execute successfully?
        ↓
      No
        ↓
Access / execution failure
        ↓
Classify failure
        ↓
Transient?
   ↓             ↓
 Yes             No
   ↓              ↓
Retry        Recovery/escalation
```

```text
Did the query execute successfully?
        ↓
      Yes
        ↓
Were there zero matches?
        ↓
      Yes
        ↓
Valid empty result
        ↓
Accept as answer
```

### Coordinator decision

```text
Subagent fails
      ↓
Structured error
      ↓
Coordinator
      ↓
Can it recover?
  ↓          ↓
Yes          No
 ↓            ↓
Recover     Continue with partial results /
            alternative / escalation
```

## Golden Rules ⭐

1. Every propagated failure should include **type, attempt, partial results, and alternatives**.
2. Distinguish transient, validation, business, and permission failures.
3. Never silently convert failures into empty successful results.
4. Never unnecessarily terminate the entire workflow because one subagent failed.
5. **Access failure ≠ valid empty result.**
6. A successful query with zero matches is a valid answer.
7. Retry transient failures when appropriate.
8. Use local recovery before propagating a recoverable transient failure.
9. The coordinator decides the next recovery action.
10. Preserve partial results when they are useful.
11. Use coverage annotations when source failure limits the final synthesis.
12. Do not retry blindly; classify the failure first.

## What to Memorise for the Exam

### Error context

```text
Type
+
Attempt
+
Partial results
+
Alternative approaches
```

### Failure types

```text
Transient
Validation
Business
Permission
```

### Critical distinction

```text
Access failure
→ Query did not execute
→ Retry/recovery may be needed

Valid empty result
→ Query executed
→ Zero matches is the answer
```

### Catastrophic anti-patterns

```text
Silent suppression
Workflow termination
```

### Retry pattern

```text
1s → 2s → 4s
```

### Final memory

> **Propagate structured errors rather than hiding failures or killing the workflow; distinguish access failures from valid empty results, recover transient errors locally, and let the coordinator use partial results, alternatives, or escalation.**

---

# Domain 5 — Task 5.4: Codebase Exploration & Context Degradation

Here are **simple, exam-focused notes** for the page you shared. I’ll keep the same structure and level of detail as your Domain 1 and Domain 2 notes.

## 1. What You Need to Know

When Claude explores a large codebase, the main problem is not necessarily the maximum token limit.

The more important problem is **context degradation**.

As verbose discovery output accumulates, the model can gradually lose its grip on:

- Exact class names
- Exact method names
- File paths
- Dependency chains
- Specific findings

The model may start replacing exact evidence with generic statements such as:

> “This is a typical service architecture.”

That shift is a sign of context degradation.

### Core memory

> **Context degradation = loss of attention quality, not simply token exhaustion.**

## 2. Context Degradation

Context degradation occurs when the model has accumulated so much discovery material that specific findings become difficult to maintain and use accurately.

### Observable symptom

Early in exploration:

```text
UserService.java
→ UserService.update()
→ PaymentService.charge()
→ PaymentRepository.save()
```

Later, the model may describe the system as:

```text
“a typical service layer with a repository.”
```

The second description is less precise.

### Why this matters

Codebase analysis requires exact references.

Losing:

- class names,
- method names,
- paths,
- dependency relationships,

can cause incorrect conclusions.

## 3. Scratchpad Files

The primary mitigation recommended by the guide is:

> **Use scratchpad files.**

A scratchpad should be maintained from the beginning of exploration.

Store important findings such as:

- Exact class names
- File paths
- Dependency chains
- Critical discoveries
- Important relationships

Example:

```text
SCRATCHPAD

src/services/UserService.ts
- updateUser()
- calls PaymentService.charge()

src/services/PaymentService.ts
- charge()
- calls PaymentRepository.save()

Critical finding:
PaymentService can be reached from UserService.updateUser().
```

### Why scratchpads work

The information is stored outside the increasingly noisy conversational context.

The model can refer back to the structured findings instead of relying only on the entire discovery history.

Memory:

> **Explore → record exact findings → refer back later.**

## 4. Subagent Delegation

Subagents are useful for codebase exploration.

However, the key benefit is **context isolation**, not merely parallelisation.

### Common misunderstanding

> “Subagents are mainly useful because they allow parallel work.”

That is incomplete.

The important architectural benefit is:

> Each subagent gets a more focused context.

Example:

```text
Coordinator
   ├── Auth subagent
   ├── Billing subagent
   └── API subagent
```

Each subagent can analyse its area without carrying the full discovery history of every other area.

### Structured subagent output

Subagents should return concise structured summaries rather than dumping all their reasoning into the coordinator's context.

## 5. Summary Injection Between Phases

Large explorations often have multiple phases.

For example:

```text
Phase 1 → Repository discovery
Phase 2 → Dependency analysis
Phase 3 → Risk analysis
```

A later phase should not always restart from zero.

Instead:

> Inject a structured summary from the earlier phase into the next phase's subagents.

### Example

Phase 1 produces:

```text
Repository summary:
- Main API entry: src/api/index.ts
- Authentication: src/auth/AuthService.ts
- Database: src/db/UserRepository.ts
- Critical dependency: API → AuthService → UserRepository
```

Phase 2 receives this summary.

### Benefit

The next phase avoids repeating the same exploration and does not have a cold start.

Memory:

> **Phase summary → next phase context.**

## 6. `/compact`

The guide recommends using `/compact` **proactively**.

Do not wait until the context is completely exhausted.

### Wrong approach

```text
Keep adding context
      ↓
Hit hard limit
      ↓
Compact
```

### Better approach

```text
Context becoming noisy
      ↓
Compact proactively
      ↓
Continue with focused context
```

### Exam trap

`/compact` is not only a last-resort response to a hard context limit.

It can be used before degradation becomes severe.

## 7. Crash Recovery with State Manifests

Long-running exploration can fail or crash.

Restarting from the beginning wastes work and can cause inconsistent analysis.

Use a **structured state manifest**.

The manifest should record:

- Explored paths
- Key findings
- Current phase
- Next steps

Example:

```text
STATE MANIFEST

Explored paths:
- src/api/
- src/services/
- src/repositories/

Key findings:
- API calls AuthService
- AuthService calls UserRepository

Current phase:
Dependency analysis

Next steps:
Analyse payment dependencies
```

### Recovery pattern

```text
Crash
 ↓
Load state manifest
 ↓
Recover explored paths + findings
 ↓
Resume current phase
 ↓
Continue next steps
```

Memory:

> **Manifest = recovery map.**

## Exam Traps

### Trap 1 — “A larger context window solves the problem.”

Wrong.

The issue is not only how many tokens fit.

The problem is maintaining attention and exactness as context becomes noisy.

Correct concept:

> Use scratchpads, context isolation, summaries, and proactive compaction.

---

### Trap 2 — “Subagents are mainly for parallelisation.”

Incomplete/wrong for this exam concept.

The primary benefit highlighted here is:

> **Context isolation.**

Parallelisation can also be useful, but it is not the key concept being tested.

---

### Trap 3 — “Use `/compact` only when the context limit is reached.”

Wrong.

Correct concept:

> Use `/compact` proactively before context degradation becomes severe.

---

### Trap 4 — “If Claude crashes, restart exploration from the beginning.”

Wrong.

Correct concept:

> Maintain a structured state manifest and resume from the saved state.

---

### Trap 5 — “Keep every discovery detail in conversation history.”

Wrong.

Verbose discovery output increases context degradation.

Correct concept:

> Preserve exact findings in scratchpad files and return structured summaries from subagents.

## Practice Scenario

### Scenario

Claude is analysing a large repository.

After several exploration phases, it starts saying:

> “The application appears to use a standard service-repository architecture.”

Earlier it had identified specific dependency chains, but it is now no longer consistently naming the classes and methods involved.

### Question

What problem is occurring?

### Correct answer

**Context degradation.**

### Why?

The model is losing grip on specific findings as verbose discovery output accumulates and is falling back to generic patterns.

### Best mitigation

Use a scratchpad containing:

- Exact file paths
- Class names
- Method names
- Dependency chains
- Critical findings

Also use context isolation, phase summaries, and proactive `/compact`.

---

### Scenario 2

A codebase exploration process crashes halfway through dependency analysis.

### Correct answer

Load a structured state manifest containing:

```text
Explored paths
Key findings
Current phase
Next steps
```

### Why?

This allows the coordinator to resume instead of repeating the entire exploration.

## Build Exercise

Build a codebase-exploration workflow:

1. Create a coordinator.
2. Delegate focused areas to subagents.
3. Give each subagent an isolated context.
4. Ask subagents to return structured summaries.
5. Create a scratchpad from the beginning.
6. Record exact class names.
7. Record exact method names.
8. Record file paths.
9. Record dependency chains.
10. Record critical findings.
11. Create a structured summary after the first exploration phase.
12. Inject the summary into the next phase.
13. Use `/compact` proactively.
14. Create a state manifest.
15. Record explored paths, findings, current phase, and next steps.
16. Compare analysis quality with and without the scratchpad.

## Decision Framework

```text
Is the codebase large and exploration-heavy?
        ↓
      Yes
        ↓
Use focused subagents
        ↓
Maintain scratchpad
        ↓
Return structured summaries
```

### During exploration

```text
Context becoming noisy?
        ↓
      Yes
        ↓
Use /compact proactively
```

### Between phases

```text
Phase completed
        ↓
Create structured summary
        ↓
Inject into next phase
```

### If the process crashes

```text
Crash
 ↓
Load state manifest
 ↓
Recover state
 ↓
Resume
```

## Golden Rules ⭐

1. **Context degradation is an attention-quality problem, not just a token-limit problem.**
2. Preserve exact findings in scratchpad files.
3. Record file paths, class names, methods, dependency chains, and critical findings.
4. Subagents provide **context isolation**, not merely parallelisation.
5. Return structured summaries from subagents.
6. Inject phase summaries into later phases.
7. Use `/compact` proactively.
8. Do not wait until the hard context limit.
9. Maintain a structured state manifest for crash recovery.
10. A state manifest should include explored paths, key findings, current phase, and next steps.
11. Avoid restarting large explorations from scratch.
12. **Scratchpad + isolation + phase summary + compact + manifest** is the core pattern.

## What to Memorise for the Exam

### Context degradation

```text
Verbose discovery
      ↓
Loss of exact findings
      ↓
Generic “typical pattern” reasoning
```

### Primary mitigation

```text
Scratchpad files
```

Preserve:

```text
Class names
Method names
File paths
Dependency chains
Critical findings
```

### Subagents

```text
Primary benefit = Context isolation
```

### Phase handoff

```text
Phase 1 summary
      ↓
Injected into Phase 2
```

### Compaction

```text
/compact
→ Use proactively
```

### Crash recovery

```text
State manifest
→ Explored paths
→ Key findings
→ Current phase
→ Next steps
```

### Final memory

> **Codebase context degrades when exact findings get buried, so use scratchpads for precision, isolated subagents and phase summaries for focused context, proactive `/compact`, and state manifests for recovery.**

---

# Domain 5 — Task 5.5: Human Review & Confidence Calibration

Here are **simple, exam-focused notes** for the page you shared. I’ll keep the same structure and level of detail as your Domain 1 and Domain 2 notes.

## 1. What You Need to Know

Human review is a safety net for automated Claude systems.

The challenge is not simply:

> “Should humans review everything?”

The real goal is:

> **Allocate limited human review capacity where it provides the most value.**

The most important exam ideas are:

- Aggregate accuracy can hide serious segment failures.
- Validate accuracy by **document type AND field segment**.
- Use **stratified random sampling**.
- Sample **high-confidence outputs too**, not only low-confidence outputs.
- Raw model confidence is not automatically calibrated.
- Use labelled ground truth to calibrate confidence.
- Route cases based on calibrated thresholds.
- Prioritise human review by uncertainty, not simply chronological order.

## 2. The Aggregate Metrics Trap

A system can appear highly accurate overall while performing badly on an important segment.

Example:

| Document type | Accuracy |
|---|---:|
| Standard invoices | ~99% |
| Handwritten receipts | ~60% |
| Scanned PDFs | ~72% |
| International formats | ~45% |

An aggregate result might still look like:

> **97% overall accuracy**

That sounds excellent.

But the segment-level results show serious failures.

### Exam point

> **97% aggregate accuracy does not automatically mean the system is safe to automate.**

Always evaluate by:

1. Document type
2. Field segment

Memory trick:

> **Aggregate hides. Segments reveal.**

## 3. Validate by Document Type AND Field Segment

Do not only measure:

```text
Overall accuracy = 97%
```

Instead measure:

```text
Document type
+
Field type/segment
+
Actual accuracy
```

Example:

| Document type | Field | Accuracy |
|---|---|---:|
| Invoice | Total | 99% |
| Handwritten receipt | Total | 60% |
| Scanned PDF | Date | 72% |
| International format | Tax ID | 45% |

This reveals where automation is actually safe.

## 4. Stratified Random Sampling

**Stratified random sampling** means dividing the data into meaningful groups, then sampling representatives from each group.

Possible strata include:

- Document type
- Confidence band
- Field type

### Why stratify?

A purely random sample may accidentally under-represent difficult segments.

Stratification ensures that important groups are evaluated.

### Two purposes

Stratified sampling is used for:

1. Ongoing accuracy measurement.
2. Detecting novel error patterns.

## 5. Sample High-Confidence Outputs Too

This is a major exam trap.

You might think:

> “Only review low-confidence extractions because high-confidence ones are already safe.”

Wrong.

High-confidence automated outputs can develop new error patterns.

Therefore:

> **High-confidence outputs must also be sampled.**

### Correct strategy

Sample across confidence bands:

```text
Low confidence
Medium confidence
High confidence
```

This lets you detect errors that the confidence system itself may not recognise.

Memory:

> **High confidence still needs a spot check.**

## 6. Raw Confidence Is Not Calibrated

Claude may assign confidence scores to extracted fields.

For example:

```text
Invoice total:
Confidence = 0.95
```

That does **not** automatically mean:

> “95% of predictions with this score are correct.”

The same confidence score can correspond to different actual accuracy levels for:

- Different fields
- Different document types

### Example

```text
Field A:
0.90 confidence → 98% actually correct

Field B:
0.90 confidence → 70% actually correct
```

Therefore:

> Raw confidence cannot automatically be used as a reliable routing threshold.

## 7. Confidence Calibration

To calibrate confidence, use a **labelled validation set** with known ground truth.

Basic process:

```text
Model confidence
       ↓
Labelled ground truth
       ↓
Compare confidence vs actual accuracy
       ↓
Calibration curve
       ↓
Calibrated thresholds
```

### Why?

You need to discover what a confidence score actually means for the system.

### Key distinction

```text
Raw confidence
≠
Calibrated confidence
```

Memory trick:

> **Confidence needs ground truth.**

## 8. Human Review Routing

After calibration, define routing thresholds.

A useful pattern is:

### Above calibrated threshold

```text
Automated processing
+
Random sampling
```

### Below calibrated threshold

```text
Human review
```

### Ambiguous zone

```text
Prioritised human review
```

This creates a hybrid system.

| Confidence/uncertainty | Action |
|---|---|
| Above calibrated threshold | Automate + sample |
| Below threshold | Human review |
| Ambiguous zone | Prioritised human review |

## 9. Prioritising Reviewer Capacity

Human review capacity is limited.

Do not simply process review cases chronologically.

Prioritise cases with the highest uncertainty.

Examples:

- Low confidence
- Ambiguous documents
- Contradictory information
- Poor historical performance
- Documents with multiple possible interpretations

### Dynamic review queue

A good queue continuously prioritises the cases most likely to benefit from human attention.

Memory:

> **Uncertainty first, chronology second.**

## 10. Correct Calibration Sequence

The guide recommends this sequence:

### Step 1

Measure accuracy by:

```text
Document type
+
Field segment
```

### Step 2

Calibrate confidence using a labelled validation set.

### Step 3

Set routing thresholds.

### Step 4

Use stratified random sampling.

### Step 5

Only then reduce human review for consistently validated segments.

### Important principle

Do not remove human review merely because the aggregate metric looks good.

Reduce review only where the relevant segment has been consistently validated.

## Exam Traps

### Trap 1 — “97% overall accuracy means the system is safe.”

Wrong.

Aggregate accuracy can hide severe failures in specific document types or fields.

Correct concept:

> Validate by document type AND field segment.

---

### Trap 2 — “Only sample low-confidence outputs.”

Wrong.

High-confidence outputs can develop novel error patterns.

Correct concept:

> Sample high-confidence outputs too.

---

### Trap 3 — “A confidence score of 0.95 means 95% accuracy.”

Wrong.

Raw confidence is not automatically calibrated.

Correct concept:

> Compare confidence against labelled ground truth.

---

### Trap 4 — “Use one confidence threshold for everything.”

Wrong.

Different fields and document types can have different actual accuracy at the same confidence score.

Correct concept:

> Calibrate confidence using representative labelled data.

---

### Trap 5 — “Review cases in chronological order.”

Wrong.

Reviewer capacity should be allocated to the cases with the highest uncertainty and risk.

Correct concept:

> Use a dynamic uncertainty-based queue.

---

### Trap 6 — “Reduce human review as soon as the model looks accurate.”

Wrong.

First validate the relevant segments consistently.

Correct concept:

> Measure → calibrate → threshold → sample → then reduce review selectively.

## Practice Scenario

### Scenario

An invoice-extraction system reports:

> 97% overall accuracy.

However:

- Standard invoices = 99%
- Handwritten receipts = 60%
- International formats = 45%

### Question

Should the company immediately automate all documents?

### Correct answer

No.

### Why?

The aggregate metric hides severe segment failures.

The system must be evaluated by document type and field segment before reducing human review.

---

### Scenario 2

The model assigns:

```text
Confidence = 0.95
```

to thousands of extracted values.

### Question

Can the company automatically route every 0.95-confidence result without validation?

### Correct answer

No.

### Why?

Raw confidence is not automatically calibrated.

Use a labelled validation set to compare confidence scores with actual accuracy.

---

### Scenario 3

The review team can only inspect 100 documents per day.

Which should be prioritised?

### Correct answer

Prioritise the highest-uncertainty cases, including:

- Low confidence
- Ambiguous documents
- Contradictory information
- Poor historical document types
- Multiple interpretations

### Why?

Limited human capacity should be directed where it has the greatest expected value.

## Build Exercise

Build a human-review calibration system:

1. Create mock extraction results.
2. Include field-level confidence scores.
3. Use at least 4 document types.
4. Measure accuracy separately by document type.
5. Measure accuracy by field segment.
6. Create a labelled validation set.
7. Compare confidence with actual accuracy.
8. Build a calibration module.
9. Create a calibration curve.
10. Define calibrated routing thresholds.
11. Create stratified random sampling.
12. Include low-, medium-, and high-confidence outputs.
13. Create a dynamic review queue.
14. Prioritise uncertainty over chronological order.
15. Validate consistently performing segments before reducing human review.

## Decision Framework

```text
Do you only know aggregate accuracy?
        ↓
      Yes
        ↓
Not enough
        ↓
Measure by document type + field segment
```

```text
Do you have raw confidence scores?
        ↓
      Yes
        ↓
Do you have labelled ground truth?
        ↓
      No
        ↓
Do not treat confidence as calibrated
```

```text
Labelled ground truth available
        ↓
Compare confidence vs actual accuracy
        ↓
Calibrate
        ↓
Set thresholds
        ↓
Route cases
```

### Review routing

```text
Above calibrated threshold
→ Automate + stratified sampling

Below threshold
→ Human review

Ambiguous zone
→ Prioritised human review
```

## Golden Rules ⭐

1. **97% aggregate accuracy ≠ safe automation.**
2. Validate by **document type AND field segment**.
3. Use stratified random sampling.
4. Sample high-confidence outputs as well as low-confidence outputs.
5. High confidence can still hide novel errors.
6. Raw confidence is not calibrated confidence.
7. Calibration requires labelled ground truth.
8. Compare confidence against actual accuracy.
9. Use calibrated thresholds for routing.
10. Prioritise human review by uncertainty.
11. Do not rely on chronological review queues.
12. Reduce human review only after consistently validated segments are demonstrated.
13. Human review is a safety net; allocate it where it adds the most value.

## What to Memorise for the Exam

### Aggregate metric trap

```text
97% overall
≠
97% for every segment
```

### Validation dimensions

```text
Document type
+
Field segment
```

### Sampling

```text
Stratified random sampling
+
High-confidence sampling
```

### Calibration

```text
Raw confidence
      ↓
Labelled ground truth
      ↓
Actual accuracy
      ↓
Calibration
      ↓
Threshold
```

### Review queue

```text
Highest uncertainty first
```

### Final memory

> **Never trust aggregate accuracy or raw confidence alone: validate by document type and field segment, calibrate confidence with labelled ground truth, sample even high-confidence outputs, and send the highest-uncertainty cases to human review.**

---

# Domain 5 — Task 5.6: Information Provenance & Multi-Source Synthesis

Here are **simple, exam-focused notes** for the page you shared. I’ll keep the same structure and level of detail as your Domain 1 and Domain 2 notes.

## 1. What You Need to Know

**Provenance** means knowing:

> **Where every claim came from and how confidently it can be supported.**

In a multi-source system, information can pass through several agents:

```text
Research
   ↓
Analysis
   ↓
Synthesis
   ↓
Final report
```

The biggest risk is that attribution is lost as information is summarised or paraphrased.

The most important exam rule is:

> **Every important finding must preserve its source mapping through the entire pipeline.**

## 2. The Five Required Provenance Fields

Every finding should carry a structured claim-source mapping containing **five required fields**:

1. **Claim**
2. **Source URL**
3. **Document name**
4. **Relevant excerpt**
5. **Publication date**

Example:

```text
Claim:
Revenue increased by 12%.

Source URL:
https://example.com/report

Document name:
Annual Financial Report 2026

Relevant excerpt:
Revenue increased by 12% year over year.

Publication date:
15 March 2026
```

### Memory trick

> **Claim + URL + Document + Excerpt + Date**

This is one of the highest-value items to memorise.

## 3. Why Attribution Gets Lost

Attribution often disappears during:

- Summarisation
- Paraphrasing
- Multi-agent handoffs
- Synthesis

Example:

### Research agent

```text
Claim A
→ Source 1
→ Date 10 March
```

### Analysis agent

```text
Claim A
→ “Research indicates...”
```

The source has disappeared.

### Final report

```text
Research indicates...
```

Now the reader cannot verify the claim.

### Correct approach

Carry the complete provenance mapping forward at every stage.

Memory:

> **Summarisation must compress wording, not provenance.**

## 4. Provenance Through Multi-Agent Synthesis

A reliable pipeline looks like this:

```text
Research subagent
      ↓
Collect claim-source mappings
      ↓
Analysis agent
      ↓
Preserve mappings
      ↓
Synthesis agent
      ↓
Merge mappings
      ↓
Report generation
      ↓
Output citations
```

### Each stage has a responsibility

| Stage | Responsibility |
|---|---|
| Research | Collect provenance |
| Analysis | Evaluate while preserving provenance |
| Synthesis | Merge and preserve mappings |
| Report generation | Render citations/references |

### Critical exam point

The **synthesis step** is the most common place where attribution is lost.

## 5. Conflicting Credible Sources

Sometimes two credible sources disagree.

Example:

```text
Source A:
Market size = $10B
Publication date = January

Source B:
Market size = $12B
Publication date = March
```

### What should you do?

Do **not**:

- Arbitrarily choose one.
- Average the values.
- Always choose the most recent source.
- Always choose the most authoritative source.

Instead:

> **Preserve both values with full attribution.**

Then explain possible reasons for the difference.

Possible explanations include:

- Different methodologies
- Different reporting periods
- Different data collection dates
- Different definitions

The final consumer/coordinator can decide how to use the conflicting information.

## 6. Temporal Awareness

Different publication dates can explain apparently conflicting information.

For example:

```text
Source A:
Data collected in January

Source B:
Data collected in March
```

The numbers may differ because the underlying situation changed.

Therefore:

> **Different dates do not automatically mean one source is wrong.**

Always preserve:

- Publication date
- Relevant time period
- Data collection context when available

Memory trick:

> **Different dates can mean different truths at different times.**

## 7. Do Not Resolve Conflicts Arbitrarily

A synthesis system should preserve the disagreement rather than silently selecting a winner.

### Wrong

```text
Source A = $10B
Source B = $12B

Final:
Market size = $12B
```

The reader has no idea that another credible source reported $10B.

### Better

```text
Source A reports $10B (January).
Source B reports $12B (March).

The difference may reflect different reporting periods or methodologies.
```

This keeps the conflict visible.

## 8. Who Decides the Conflict?

The analysis agent should not arbitrarily decide which credible source is “correct”.

Instead:

> Preserve the conflict and let the coordinator/consumer make the final decision.

The system should provide enough provenance and temporal context for the decision.

## 9. Well-Established vs Contested Findings

Provenance also helps distinguish between:

### Well-established finding

Supported independently by multiple sources.

For example:

```text
3 independent sources
→ Same conclusion
```

### Contested finding

Supported by limited evidence or conflicting sources.

For example:

```text
1 source
→ Claim

2 credible sources
→ Contradictory claim
```

The final report should reflect that difference.

### Exam point

Do not present a contested finding with the same certainty as a well-established finding.

## 10. Content-Appropriate Rendering

Different information types should be presented in the format that best communicates them.

### Financial data

Use:

> **Tables**

Example:

| Year | Revenue |
|---|---:|
| 2025 | £10M |
| 2026 | £12M |

### News / current events

Use:

> **Prose**

Narrative explanation is usually clearer for developments and events.

### Technical findings

Use:

> **Structured lists**

Example:

```text
- API endpoint affected
- Authentication dependency
- Error condition
- Recommended action
```

### Memory trick

> **Financial → Table**  
> **News → Prose**  
> **Technical → List**

## 11. Multi-Source Provenance Pipeline

A complete architecture can look like:

```text
Research Subagent 1
       ↓
Claim-source mappings
       ↓

Research Subagent 2
       ↓
Claim-source mappings
       ↓

Synthesis
       ↓
Merge mappings + resolve presentation
       ↓

Final Report
       ↓
Inline citations / structured references
```

### Important requirement

When two research agents provide related claims, the synthesis agent must preserve both source mappings where appropriate.

Do not collapse multiple sources into one unattributed statement.

## 12. Build Exercise

Build a provenance-aware multi-source synthesis system:

1. Create a provenance schema.
2. Require these five fields:
   - Claim
   - Source URL
   - Document name
   - Relevant excerpt
   - Publication date
3. Create two research subagents.
4. Make each subagent return claim-source mappings.
5. Pass their results to a synthesis agent.
6. Ensure the synthesis agent preserves the mappings.
7. Create a conflicting-source test.
8. Preserve both conflicting values.
9. Include dates and possible methodological/reporting-period explanations.
10. Do not automatically choose the newest source.
11. Do not average conflicting values.
12. Render financial findings as tables.
13. Render news/current events as prose.
14. Render technical findings as structured lists.
15. Verify that final outputs retain citations/references.

## Exam Traps

### Trap 1 — “Choose the most recent source.”

Wrong.

Recency alone does not resolve a conflict.

Correct concept:

> Preserve both sources and explain possible temporal/methodological differences.

---

### Trap 2 — “Average conflicting credible values.”

Wrong.

Averaging hides the disagreement and may have no methodological justification.

Correct concept:

> Preserve both values with full provenance.

---

### Trap 3 — “Always choose the most authoritative source.”

Wrong.

Authority alone does not automatically resolve methodological or temporal differences.

Correct concept:

> Preserve the conflict and let the consumer/coordinator decide.

---

### Trap 4 — “Summarisation can remove source details.”

Wrong.

Attribution commonly dies during summarisation and synthesis.

Correct concept:

> Preserve claim-source mappings through every transformation.

---

### Trap 5 — “The synthesis agent can decide which source is correct.”

Wrong.

The synthesis agent should preserve the conflict and provide context.

Correct concept:

> The coordinator/consumer makes the final decision.

---

### Trap 6 — “One citation at the end is enough for all claims.”

Not necessarily.

The system should maintain structured claim-source mappings so individual claims can be traced to their sources.

## Practice Scenario

### Scenario

Two credible research sources report different market sizes.

```text
Source A:
$10 billion
Published January

Source B:
$12 billion
Published March
```

### Question

What should the synthesis agent do?

### Correct answer

Preserve both values with their full source attribution and dates, then explain that differences may be caused by:

- Different reporting periods
- Different methodologies
- Different data collection dates

### Why?

The system should not arbitrarily select one, average the values, or automatically prefer the newest source.

---

### Scenario 2

A research agent returns:

```text
Claim:
Revenue increased 12%.

Source:
Not provided.
```

### Question

Can this claim safely enter the final report as a sourced finding?

### Correct answer

No.

### Why?

The provenance mapping is incomplete.

The required fields include:

```text
Claim
Source URL
Document name
Relevant excerpt
Publication date
```

## Decision Framework

### For every finding

```text
Do I have a claim?
        ↓
Do I have source URL?
        ↓
Do I have document name?
        ↓
Do I have relevant excerpt?
        ↓
Do I have publication date?
        ↓
Complete provenance
```

### For conflicting sources

```text
Sources disagree
      ↓
Preserve both
      ↓
Attach attribution + dates
      ↓
Explain possible methodology/time differences
      ↓
Let consumer/coordinator decide
```

### For content rendering

```text
Financial data
→ Table

News/current events
→ Prose

Technical findings
→ Structured list
```

## Golden Rules ⭐

1. Provenance means knowing where every claim came from.
2. Every claim mapping requires **Claim + URL + Document + Excerpt + Date**.
3. Preserve provenance through summarisation and paraphrasing.
4. The synthesis stage is the most common place for attribution loss.
5. Downstream agents must preserve and merge source mappings.
6. Never arbitrarily choose between conflicting credible sources.
7. Never average conflicting values without justification.
8. Do not automatically choose the newest or most authoritative source.
9. Preserve both conflicting values with dates and attribution.
10. Explain possible methodological or reporting-period differences.
11. Let the coordinator/consumer decide how to resolve the conflict.
12. Distinguish well-established findings from contested findings.
13. Financial data → tables.
14. News/current events → prose.
15. Technical findings → structured lists.

## What to Memorise for the Exam

### Five provenance fields

```text
1. Claim
2. Source URL
3. Document name
4. Relevant excerpt
5. Publication date
```

### Most common attribution-loss point

```text
Synthesis
```

### Conflict rule

```text
Conflict
→ Preserve both
→ Keep attribution
→ Keep dates
→ Explain possible reasons
→ Let consumer decide
```

### Rendering rule

```text
Financial → Table
News → Prose
Technical → Structured list
```

### Provenance pipeline

```text
Research
→ Analysis
→ Synthesis
→ Report
```

At every stage:

> **Preserve the claim-source mapping.**

### Final memory

> **Every claim needs Claim + URL + Document + Excerpt + Date; preserve provenance through every agent and synthesis step, and when credible sources conflict, keep both with temporal/methodological context instead of arbitrarily choosing one.**

---

# Agent Roles — Simple Exam Cheat Sheet

Use these simple definitions when an exam question asks **which agent should perform a task**.

**Coordinator Agent** — plans the work and assigns tasks → *“You handle web research, you analyse the data.”*

**Web/Search Agent** — searches and retrieves information → *“Find the latest Claude documentation.”*

**Research Agent** — investigates a specific topic deeply → *“Research Claude MCP security practices.”*

**Analysis Agent** — analyses data/findings and identifies insights → *“Analyse these sales numbers and find trends.”*

**Coding Agent** — writes, modifies, and tests code → *“Fix this Python API bug.”*

**Document Agent** — reads and extracts information from documents → *“Extract the key requirements from this PDF.”*

**Verification Agent** — checks whether information/claims are correct → *“Verify this claim against the source.”*

**Synthesis Agent** — combines findings from multiple agents into one result → *“Combine the web, contract, and finance findings into one recommendation.”*

**Response Agent** — turns the final findings into the user-facing answer → *“Present the recommendation clearly to the customer.”*

## Quick Exam Memory

> **Coordinator = Plan**  
> **Web/Search = Find**  
> **Research = Investigate**  
> **Analysis = Understand**  
> **Coding = Build/Fix**  
> **Document = Extract**  
> **Verification = Check**  
> **Synthesis = Combine**  
> **Response = Present**

### Simple Multi-Agent Flow

```text
User request
    ↓
Coordinator Agent
    ↓
Assign specialised tasks
    ↓
Web/Search / Research / Document / Coding
    ↓
Analysis Agent
    ↓
Verification Agent
    ↓
Synthesis Agent
    ↓
Response Agent
    ↓
User
```

### Exam Decision Rule

```text
Need to plan/delegate?
→ Coordinator

Need to search the web?
→ Web/Search

Need deep investigation?
→ Research

Need to analyse findings/data?
→ Analysis

Need to write/fix/test code?
→ Coding

Need to read/extract from a document?
→ Document

Need to check whether a claim is correct?
→ Verification

Need to combine multiple findings?
→ Synthesis

Need to produce the final user-facing answer?
→ Response
```

### Important Distinctions

| If the question says... | Think... |
|---|---|
| “Assign tasks to agents” | **Coordinator** |
| “Find information online” | **Web/Search** |
| “Investigate this topic deeply” | **Research** |
| “Find trends/insights in data” | **Analysis** |
| “Write or debug code” | **Coding** |
| “Extract information from a PDF/document” | **Document** |
| “Check whether this claim is true” | **Verification** |
| “Combine several agents' findings” | **Synthesis** |
| “Give the final answer to the user” | **Response** |

### Agent Role Exam Trap

Do not confuse **Research**, **Analysis**, **Verification**, and **Synthesis**:

- **Research** = gathers/investigates information.
- **Analysis** = interprets information and finds insights.
- **Verification** = checks correctness against evidence/source.
- **Synthesis** = combines multiple findings into one coherent result.

Memory trick:

> **Research finds → Analysis understands → Verification checks → Synthesis combines → Response presents.**

---

