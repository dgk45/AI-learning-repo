# Domain 4 — Prompt Engineering

## Domain 4 — Task 4.1: System Prompts with Explicit Criteria

System prompts are one of the most important ways to make Claude's behaviour consistent in production. The key is to replace vague instructions with explicit, categorical criteria that define exactly what Claude should flag, report, and skip.

## 1. What You Need to Know

### 1.1 The biggest production prompt-engineering mistake

Vague instructions such as:

- “Be conservative”
- “Only report high-confidence findings”
- “Use your best judgement”

do not define what the model should actually do.

A better approach is to provide **explicit categorical criteria**.

For example:

- Flag comments only when the claimed behaviour contradicts the actual code.
- Report bugs and security vulnerabilities.
- Skip minor style issues and local coding patterns unless they are explicitly in scope.

### 1.2 Why explicit criteria matter

Explicit criteria make the model's decisions:

- more consistent
- easier to evaluate
- easier to debug
- easier to improve

The goal is not simply to make the model “more careful”. The goal is to define the decision boundary clearly.

### 1.3 High false positives can destroy trust

If one category produces many false positives, users can start distrusting the entire system.

For example:

- Documentation mismatch → 40% false positives
- Security findings → reliable
- Bug findings → reliable

Users may eventually stop trusting **all** findings because one category is noisy.

Therefore, production systems should prioritise **system-wide trust over completeness**.

### 1.4 Temporarily disable high-false-positive categories

When a category generates too many false positives:

1. Identify the problematic category.
2. Temporarily disable it.
3. Refine its criteria.
4. Add concrete examples.
5. Re-enable it after validation.

This is better than keeping a noisy detector active simply because it finds some true positives.

### 1.5 Calibrate severity using concrete code examples

Severity definitions should be demonstrated with concrete examples rather than only prose.

For example:

| Severity | Example |
|---|---|
| Critical | A security vulnerability that exposes sensitive data |
| Major | A real bug that breaks expected behaviour |
| Minor | A small issue with limited impact |

The examples give Claude a clearer boundary between severity levels.

### 1.6 Confidence is not a substitute for criteria

Confidence-based filtering is often poorly calibrated.

Do not replace explicit criteria with:

> “Only report findings where confidence is above 90%.”

Instead:

1. Define explicit criteria.
2. Use confidence later for routing or prioritisation.

The key hierarchy is:

**Explicit criteria first → confidence-based routing second**

## Exam Traps

| Wrong answer / approach | Why it is wrong | Correct concept |
|---|---|---|
| “Tell Claude to be conservative” | Too vague to create a reliable decision boundary | Use explicit categorical criteria |
| Use a confidence threshold instead of defining criteria | Confidence is not a substitute for a clear definition | Define criteria first |
| Keep a high-FP category active because it still finds some real issues | High false positives can destroy system-wide trust | Temporarily disable and refine the category |
| Define severity only with prose | Prose can leave boundaries ambiguous | Use concrete code examples |

## Practice Scenario

### Scenario

A code-review system reports documentation mismatches. Testing shows that around **40% of these findings are false positives**.

### Question

What should you do?

### Correct Answer

**Temporarily disable the documentation-mismatch category and refine it using explicit criteria and concrete examples.**

### Explanation

The category is damaging trust because its false-positive rate is too high. Refining the criteria before re-enabling it is better than keeping a noisy detector active.

## Build Exercise

- Start with a vague baseline prompt.
- Rewrite it using explicit criteria.
- Add concrete code examples for critical, major, and minor findings.
- Compare false-positive rates.
- Temporarily disable any category with more than **25% false positives**.
- Refine the category before re-enabling it.

The guide indicates that vague prompts may produce around **30–50% inconsistency**, while an explicit-criteria version should target **below 15%** and remain stable across repeated runs.

## Decision Framework

When designing a production system prompt:

1. **Can the instruction be interpreted in multiple ways?**
   - Yes → make it explicit.
2. **Is the category producing many false positives?**
   - Yes → temporarily disable and refine it.
3. **Are severity boundaries unclear?**
   - Add concrete examples.
4. **Are you relying mainly on confidence?**
   - Define explicit criteria first.
5. **Do you need routing after the criteria are established?**
   - Use confidence as a secondary mechanism.

## Golden Rules ⭐

- **Explicit criteria beat vague instructions.**
- **Concrete examples improve severity calibration.**
- **High false positives can destroy trust in the whole system.**
- **Temporarily disabling a noisy category is better than shipping unreliable findings.**
- **Confidence is for routing after explicit criteria, not a replacement for them.**

## What to Memorise for the Exam

- Vague instructions such as “be conservative” are unreliable.
- Use explicit categorical criteria.
- High false positives in one category can damage trust in all categories.
- Temporarily disable high-FP categories while refining them.
- Calibrate severity with concrete code examples.
- Do not use confidence thresholds as a substitute for explicit criteria.
- Remember: **Explicit criteria first → confidence-based routing second.**

### Final memory

**Define exactly what to flag, report, and skip; refine noisy categories before they destroy trust; then use confidence for routing.**


## Domain 4 — Task 4.2: Few-Shot Prompting

Few-shot examples are one of the most effective techniques for making Claude produce consistent, well-formatted output when instructions alone are not enough.

## 1. What You Need to Know

### 1.1 When to use few-shot prompting

Use few-shot examples when you see one of these three problems:

1. **Detailed instructions but inconsistent formatting**
   - The model understands the task but produces different output structures.

2. **Inconsistent judgement calls on ambiguous cases**
   - The model makes different decisions for similar situations.

3. **Extraction returns empty or null values even though the information exists**
   - This is especially common when documents use different structures or wording.

### 1.2 Use 2–4 targeted examples

Do not simply add many examples.

A good few-shot prompt normally uses **2–4 targeted examples** that focus on the exact failure scenarios you want to correct.

The examples should be:

- representative
- targeted
- clearly formatted
- relevant to the difficult cases

### 1.3 Include reasoning, not only input-output pairs

A strong few-shot example should teach Claude **why** the decision was made.

Instead of only:

> Input → Output

use:

> Input → Reasoning → Output

The reasoning teaches the **decision principle**, which helps Claude generalise to new cases.

### 1.4 Target the actual failure scenarios

Few-shot examples should address the specific problem.

For example:

- Formatting inconsistency → show the required format.
- Ambiguous judgement → show what should be flagged and what should be ignored.
- Extraction failure → show how the same information appears in different document structures.

### 1.5 Few-shot prompting can improve extraction

When documents have varied structures, Claude may miss information or return empty fields.

Few-shot examples can show Claude that the same information may appear in different forms.

This can reduce extraction failures and hallucination of values.

### 1.6 Few-shot prompting can reduce false positives

For classification or review tasks, show both:

- what should be flagged
- what should be ignored

For example, a variable-shadowing issue may be classified as **minor** because it has limited scope and does not create a bug.

Only flag it if style consistency is actually part of the task.

## 2. Choosing the Right Technique

| Problem | Better technique |
|---|---|
| Inconsistent output formatting | Few-shot examples |
| Malformed JSON | `tool_use` + JSON schemas |
| Fabricated values for missing fields | Optional/nullable schema |
| Wrong tool selection | Better tool descriptions first, then few-shot if needed |
| Narrative extraction miss | Few-shot examples |
| Sum mismatch | Validation-retry loop |

The important exam distinction is that few-shot prompting is not a universal solution.

## Exam Traps

| Wrong answer / approach | Why it is wrong | Correct concept |
|---|---|---|
| Keep adding instructions when formatting is inconsistent | The problem may be better solved by showing examples | Use targeted few-shot examples |
| Few-shot prompting only works for literal matching | Examples teach decision principles and generalisation | Include reasoning-rich examples |
| Use confidence thresholds for inconsistent judgement | Confidence does not teach the desired decision boundary | Show positive and negative examples |
| Use few-shot for malformed JSON | Schema-based structured output is more reliable | Use `tool_use` + JSON schema |

## Practice Scenario

### Scenario

A document-extraction system works well on structured tables but frequently returns empty fields when the same information appears inside narrative paragraphs.

### Question

What is the best first improvement?

### Correct Answer

**Add targeted few-shot examples showing how to extract the information from narrative text, including both the reasoning and expected output.**

### Explanation

The model needs examples of how the target information appears in different document structures. Few-shot prompting teaches the general extraction pattern rather than relying only on explicit instructions.

## Build Exercise

1. Create a base prompt and test it against **10 varied documents**.
2. Record extraction failures.
3. Identify the specific failure patterns.
4. Create **3 reasoning-rich examples** targeting those failures.
5. Rerun the same documents.
6. Compare:
   - empty-field rate
   - format consistency
   - extraction accuracy
7. Identify which remaining problems require other techniques such as schemas or validation-retry loops.

## Decision Framework

When deciding whether to use few-shot prompting:

1. **Is the model's output format inconsistent?**
   - Yes → add targeted format examples.
2. **Are judgement calls inconsistent?**
   - Yes → show positive and negative examples with reasoning.
3. **Is extraction failing across different document structures?**
   - Yes → show examples covering those structures.
4. **Is the problem malformed JSON?**
   - Use structured tool/schema output instead.
5. **Is the problem a mathematical or semantic mismatch?**
   - Use validation and retry.

## Golden Rules ⭐

- **Use 2–4 targeted examples.**
- **Target examples at the actual failure scenarios.**
- **Include reasoning, not just input and output.**
- **Show both what to flag and what to ignore when judgement is involved.**
- **Few-shot examples teach generalisation, not just literal matching.**
- **Use the right technique for the failure; few-shot is not a universal fix.**

## What to Memorise for the Exam

Remember the three major triggers:

**Format → Judgement → Extraction**

- Inconsistent formatting → few-shot.
- Inconsistent ambiguous judgement → few-shot.
- Extraction misses information in varied structures → few-shot.
- Use **2–4 targeted examples**.
- Include **Input + Reasoning + Output**.
- Reasoning teaches the decision principle and generalisation.

### Final memory

**If instructions are not enough, show Claude 2–4 targeted examples that demonstrate the format, judgement, or extraction pattern you want.**


## Domain 4 — Task 4.3: Structured Output with Tool Use

When reliable schema-compliant output is required, structured tool use is more dependable than asking Claude to produce JSON through prompting alone.

## 1. What You Need to Know

### 1.1 Reliability hierarchy

For guaranteed schema-compliant output, the exam guide expects this hierarchy:

1. **`tool_use` + JSON schemas**
2. **Prompt-based JSON**

The key exam answer is:

**`tool_use` + JSON schema is more reliable than asking the model to output JSON in the prompt.**

### 1.2 What schemas solve — and what they do not

`tool_use` + JSON schema can eliminate **JSON syntax errors**.

However, it does **not** eliminate semantic errors.

For example, the schema cannot automatically prevent:

- a sum discrepancy
- a value being placed in the wrong field
- fabricated information

This gives the key distinction:

**Schema = shape**

**Validation = meaning**

### 1.3 `tool_choice`

The guide describes three important `tool_choice` modes.

| Mode | Behaviour |
|---|---|
| `"auto"` | Claude may call a tool or return normal text |
| `"any"` | Claude must call a tool, but can choose which tool |
| Forced named tool | Claude must call the specific named tool |

### 1.4 `tool_choice` applies per request

A forced tool choice should not normally remain forced for the next request.

For example:

1. Request 1 → force `extract_invoice`.
2. Claude calls the tool.
3. Request 2 → use `"auto"` or omit the forced choice.

Otherwise, the workflow can repeatedly force the same tool and create a loop.

### 1.5 Optional and nullable fields prevent fabrication

If a document may not contain a field, do not make that field unnecessarily mandatory.

Instead, allow:

- optional fields
- nullable fields

This lets Claude return `null` when the information is genuinely absent rather than inventing a value.

### 1.6 Use `unclear` for genuine ambiguity

When a value exists but cannot be confidently classified, an explicit enum such as:

`unclear`

can represent the uncertainty safely.

### 1.7 Use `other` for extensible categories

If the category list may grow, use:

- `other`
- a detail string

This avoids forcing Claude into an incorrect predefined category.

### 1.8 Normalise formats in the prompt and schema

Schemas should define structure, while the prompt can reinforce format normalisation.

For example:

- Dates → ISO 8601
- Enumerated values → predefined allowed values
- Amounts → consistent representation

## Exam Traps

| Wrong answer / approach | Why it is wrong | Correct concept |
|---|---|---|
| “JSON schema prevents all errors” | Schema mainly guarantees structure, not semantic correctness | Validate semantics separately |
| Confuse `"auto"` and `"any"` | They have different tool-calling requirements | `"auto"` allows text or tool; `"any"` requires a tool |
| Force the same tool on every request | Can cause repeated tool calls/loops | Force only when needed, then return to `"auto"`/omit |
| Make every field required | Missing information may cause fabrication | Use optional/nullable fields |
| Use confidence as the schema | Confidence does not define the output structure | Define explicit schema fields |

## Practice Scenario

### Scenario

An invoice extraction schema requires every invoice to contain an invoice date and department. Some source documents do not contain either field. Claude starts inventing dates and departments.

### Question

What should you change?

### Correct Answer

**Make fields that may genuinely be absent optional/nullable so Claude can return `null` instead of fabricating values.**

### Explanation

A schema should represent the actual information availability of the source. Required fields for unavailable information can encourage fabricated values.

## Build Exercise

Create a structured extraction schema with:

- 3 required fields
- 3 optional/nullable fields
- an `unclear` enum
- an `other` category
- a detail string for `other`

Then:

1. Test normal `"auto"` behaviour.
2. Test `"any"`.
3. Force a named tool.
4. Process **5 documents**.
5. Verify that genuinely missing values become `null` rather than fabricated values.
6. Add semantic validation for values such as totals and dates.

## Decision Framework

When designing structured output:

1. **Do you need reliable schema-compliant output?**
   - Yes → use `tool_use` + JSON schema.
2. **Can a field genuinely be absent?**
   - Yes → optional/nullable.
3. **Can a category be ambiguous?**
   - Yes → use `unclear`.
4. **Can categories expand?**
   - Yes → use `other` + detail.
5. **Do you need semantic correctness?**
   - Yes → add validation; schema alone is insufficient.
6. **Do you need to force a specific tool for one request?**
   - Use a named forced tool, then return to `"auto"`/omit on the next request.

## Golden Rules ⭐

- **`tool_use` + JSON schema beats prompt-based JSON for schema reliability.**
- **Schema controls shape; validation controls meaning.**
- **`"auto"` allows tool or text.**
- **`"any"` requires a tool but lets Claude choose which one.**
- **A forced named tool requires that specific tool.**
- **Do not keep a forced tool choice active across requests unnecessarily.**
- **Use optional/nullable fields when information may be absent.**
- **Use `unclear` for genuine ambiguity and `other` for extensibility.**

## What to Memorise for the Exam

- Reliability hierarchy:
  **`tool_use` + JSON schema → prompt-based JSON**
- Schema does not prevent semantic errors.
- `"auto"` = tool or text.
- `"any"` = must call a tool.
- Forced named tool = must call that tool.
- Tool choice is per request.
- Missing information → optional/nullable.
- Ambiguous information → `unclear`.
- Extensible categories → `other` + detail.
- **Schema = shape; validation = meaning.**

### Final memory

**Use tool schemas to guarantee the output shape, then use validation to guarantee the meaning.**


## Domain 4 — Task 4.4: Validation, Retry, and Feedback Loops

Production extraction will sometimes fail. Validation-retry loops turn these failures into a self-correcting workflow by feeding precise validation errors back to Claude.

## 1. What You Need to Know

### 1.1 The three components of an effective retry

A useful retry should send Claude:

1. **The original document**
2. **The failed extraction**
3. **The specific validation error**

The memory pattern is:

**Document + Output + Error**

A vague retry such as:

> “Try again.”

often causes Claude to reproduce the same mistake.

### 1.2 What retries can fix

Retries work well when the information exists but the extraction is wrong.

They can fix:

- format mismatches
- structural errors
- misplaced values
- mathematical errors

### 1.3 What retries cannot fix

Retries cannot create information that does not exist.

They cannot fix cases where:

- the information is genuinely absent
- an external document was not provided
- required knowledge is unavailable

In those situations:

- send to human review, or
- return `null` if the schema permits it.

### 1.4 Self-correction schema design

A schema can expose information needed for validation and correction.

Useful fields include:

#### `calculated_total` vs `stated_total`

These allow the system to detect whether the calculated amount matches the amount stated in the document.

#### `total_discrepancy`

This makes the mismatch explicit.

#### `conflict_detected`

A boolean can indicate contradictory statements in the source.

#### `detected_pattern`

In code-review systems, this can record patterns such as repeated findings and developer dismissals. These signals can feed a later prompt-refinement loop.

### 1.5 Schema errors vs semantic errors

This distinction is essential.

| Error type | Example | Best mechanism |
|---|---|---|
| Schema/syntax error | Malformed JSON | `tool_use` + JSON schema |
| Schema/syntax error | Missing required field | JSON schema |
| Schema/syntax error | Wrong data type | JSON schema |
| Semantic error | Wrong sum | Validation + retry |
| Semantic error | Wrong date ordering | Validation + retry |
| Semantic error | Value placed in wrong field | Validation + retry |

Therefore:

**Schema handles structure. Validation handles meaning.**

### 1.6 Pydantic validation

Pydantic can validate:

- structure
- semantic cross-field rules

It can also produce specific validation errors that can be passed back to Claude for retry.

The current SDK approach described in the guide uses structured parsing such as:

`messages.parse(..., output_format=Invoice)`

which can provide a Pydantic instance through `parsed_output`.

The guide also notes that `strict: true` can enforce schema-conformant inputs server-side, but semantic validation is still necessary.

## Exam Traps

| Wrong answer / approach | Why it is wrong | Correct concept |
|---|---|---|
| Assume retries always fix errors | Retries cannot recover information that is absent | Retry only fixable extraction/validation errors |
| Retry with “try again” | Claude may repeat the same mistake | Include the specific validation error |
| Rely only on the schema | Schema does not validate semantic correctness | Add semantic validation |
| Treat Pydantic as redundant | Pydantic can enforce cross-field semantic rules and provide useful errors | Use validation to drive correction |
| Retry missing information repeatedly | The model cannot extract information that does not exist | Return `null` or human review |

## Practice Scenario

### Scenario

Two documents are processed:

- **Document A:** line items total **£450**, but the stated total is **£500**.
- **Document B:** the department field is not present anywhere in the document.

### Question

What should the system do?

### Correct Answer

**Retry Document A with the specific validation error; send Document B to human review or return `null`.**

### Explanation

Document A contains enough information to calculate the correct total, so the discrepancy is fixable. Document B does not contain the requested information, so retrying cannot create it.

## Build Exercise

1. Define schema fields for the extraction.
2. Implement semantic validation checks.
3. Generate specific validation errors.
4. Build the retry message containing:
   - original document
   - failed output
   - validation error
5. Test with **5 documents**:
   - 2 fixable errors
   - 3 unfixable cases
6. Track `detected_pattern` and developer dismissal rates where relevant.
7. Refine prompts based on recurring failure patterns.

## Decision Framework

When validation fails:

1. **Is the information present in the source?**
   - Yes → retry with the specific error.
   - No → do not retry.
2. **Is the problem structural?**
   - Yes → use schema/tool validation.
3. **Is the problem semantic?**
   - Yes → use validation + retry.
4. **Can the source contain contradictory information?**
   - Add conflict detection.
5. **Is the same error recurring?**
   - Record the pattern and refine the prompt.

## Golden Rules ⭐

- **Effective retry = Document + Output + Error.**
- **“Try again” is not enough.**
- **Retry when the information exists but extraction is wrong.**
- **Do not retry when the information is genuinely absent.**
- **Schema catches structure; validation catches semantic errors.**
- **Use specific validation errors to drive correction.**
- **Use recurring failure patterns as feedback for prompt refinement.**

## What to Memorise for the Exam

- Retry message must contain:
  **original document + failed extraction + specific validation error**
- Fixable:
  - format
  - structure
  - misplaced values
  - mathematical errors
- Not fixable:
  - genuinely absent information
  - missing external documents
  - unavailable knowledge
- Schema errors → schema/tool.
- Semantic errors → validation-retry.
- Pydantic can support structural and cross-field semantic validation.
- Missing data → `null` or human review.

### Final memory

**If the information exists but the extraction is wrong, retry with the exact error; if the information does not exist, do not retry.**


## Domain 4 — Task 4.5: Batch Processing Strategies

The Message Batches API is primarily a **cost-optimisation strategy** for workloads that can tolerate latency.

## 1. What You Need to Know

### 1.1 Why use batch processing?

The guide describes the Message Batches API as providing:

- **50% cost savings** compared with synchronous calls
- up to a **24-hour processing window**
- **no guaranteed latency SLA**
- `custom_id` for request/response correlation

The core idea is:

**Use batch when you do not need an immediate response.**

### 1.2 Synchronous vs batch

| Workflow | Recommended approach | Reason |
|---|---|---|
| Pre-merge checks | Synchronous | Someone/system is waiting |
| Real-time code review | Synchronous | Blocking workflow |
| Overnight reports | Batch | Latency-tolerant |
| Weekly audits | Batch | Latency-tolerant |
| Nightly test generation | Batch | No immediate response required |
| Batch document extraction | Batch | Can process asynchronously |

The exam distinction is:

**Blocking workflow → synchronous**

**Latency-tolerant workflow → batch**

### 1.3 Understand the processing window

Suppose a workflow has a **30-hour SLA**.

If batch processing can take up to **24 hours**:

**30 − 24 = 6 hours of buffer**

If you check every 6 hours, there is effectively no safety margin.

The guide recommends checking every **4 hours** within the buffer.

### 1.4 `custom_id`

Use `custom_id` to correlate:

- submitted requests
- completed responses
- failed responses

This is essential when processing many independent requests asynchronously.

### 1.5 Batch failure handling

Do not blindly resubmit the entire batch.

Instead:

1. Identify failures using `custom_id`.
2. Resubmit **only the failed requests**.
3. Modify the failing prompts/input as needed.
4. Refine the approach using a sample set before the next full batch.

### 1.6 Common modifications for failed requests

Possible improvements include:

- chunk oversized documents
- simplify the extraction prompt
- add format-specific few-shot examples
- increase `max_tokens` when appropriate

### 1.7 Batch and multi-turn tool calling

The exam guide expects that **client-tool multi-turn calling is not supported within a single batch request**.

If your own code must call an external/client tool during the workflow, use synchronous processing for that step.

The current API documentation may distinguish server-side and client-side tool behaviour, but for the exam, follow the guide's stated expectation.

### 1.8 Optimise prompts before running a large batch

Do not immediately send thousands of documents.

Use this workflow:

1. Select **5–10 representative documents**.
2. Iterate on:
   - prompt
   - few-shot examples
   - schema
3. Run the full batch.
4. Retry only failures.

This reduces the cost of discovering prompt problems at large scale.

### 1.9 Retry economics

Suppose a batch contains **1,000 documents**.

#### 90% success

- 900 succeed
- 100 fail
- 100 retries

#### 60% success

- 600 succeed
- 400 fail
- 400 retries

The second case produces **4× as many resubmissions**.

Therefore, prompt optimisation before the full batch has significant cost impact.

## Exam Traps

| Wrong answer / approach | Why it is wrong | Correct concept |
|---|---|---|
| Switch everything to batch | Some workflows require immediate/blocking responses | Use synchronous for blocking workflows |
| Assume batch results arrive quickly | Batch has a processing window and no guaranteed latency SLA | Use it only when latency is acceptable |
| Use batch for client-tool multi-turn workflows | Exam guide says this is not supported within one batch request | Use synchronous for that step |
| Retry the entire batch | Wastes cost and processing | Retry only failed requests |
| Skip sample testing | Prompt failures become expensive at full scale | Sample → refine → full batch |

## Practice Scenario

### Scenario

You have two workflows:

1. Pre-merge code checks that developers are waiting for.
2. An overnight technical-debt report that can arrive the next morning.

### Question

Which should use batch processing?

### Correct Answer

**Use batch for the overnight technical-debt report and synchronous processing for the pre-merge checks.**

### Explanation

The pre-merge check is blocking. The overnight report is latency-tolerant.

## Build Exercise

1. Classify **5 workflows** as synchronous or batch.
2. Create a **20-document batch**.
3. Give every request a unique `custom_id`.
4. Identify failed requests.
5. Retry only those failures.
6. Calculate the buffer for a **30-hour SLA**.
7. Use a **5-document sample** to refine prompts before running the full batch.
8. Test how few-shot examples, schema changes, and prompt simplification affect failure rates.

## Decision Framework

Ask:

1. **Is someone or something waiting for the response?**
   - Yes → synchronous.
2. **Can the result arrive later?**
   - Yes → batch.
3. **Does the workflow require client-tool multi-turn continuation?**
   - Yes → synchronous for that step.
4. **Did some batch requests fail?**
   - Retry only those using `custom_id`.
5. **Is the prompt untested at scale?**
   - Sample first.
6. **Do you have an SLA?**
   - Calculate the maximum processing window and preserve a safety buffer.

## Golden Rules ⭐

- **Blocking → Sync.**
- **Latency-tolerant → Batch.**
- **Use `custom_id` for correlation.**
- **Retry only failed requests.**
- **Sample → refine → full batch → retry failures.**
- **Do not assume batch gives immediate results.**
- **Optimise before large-scale processing.**
- **The guide expects no client-tool multi-turn continuation inside one batch request.**

## What to Memorise for the Exam

- Message Batches API = cost optimisation.
- Guide figure: **50% cost savings**.
- Processing window: up to **24 hours**.
- No guaranteed latency SLA.
- `custom_id` correlates requests and responses.
- Blocking workflow → synchronous.
- Latency-tolerant workflow → batch.
- 30-hour SLA − 24-hour maximum processing = **6-hour buffer**.
- Guide recommends checking every **4 hours** in that buffer.
- Retry only failures.
- Use representative samples before full batch processing.

### Final memory

**Use synchronous calls when someone is waiting; use batch when latency is acceptable, correlate with `custom_id`, and retry only failures.**


## Domain 4 — Task 4.6: Multi-Instance and Multi-Pass Review

High-reliability review systems should avoid asking the same Claude session to review its own work. Independent instances and multi-pass architectures provide stronger challenge and better coverage.

## 1. What You Need to Know

### 1.1 Why same-session self-review is weak

When Claude reviews its own previous output in the same session, it retains the original reasoning context.

That makes it less likely to genuinely challenge its earlier decisions.

A prompt such as:

> “Review your answer carefully.”

does not create true independence.

The exam expects a **separate model instance / fresh context** for independent review.

### 1.2 Independent invocation gives fresh judgement

A stronger pattern is:

1. Instance A generates the result.
2. Instance B receives the result and reviews it independently.
3. Instance B does not inherit the reasoning path that produced the original answer.

The key memory:

**Same session = remembers why**

**Fresh instance = fresh judgement**

### 1.3 The attention-dilution problem

Large multi-file reviews can suffer from **attention dilution**.

Typical symptoms include:

- uneven review depth
- bugs missed in the middle of the context
- contradictory findings
- important details receiving less attention

Simply giving Claude a larger context window does not automatically solve the quality problem.

### 1.4 Two-pass review architecture

For large multi-file reviews, use two passes.

#### Pass 1 — Per-file local analysis

Analyse each file independently with a focused prompt.

This improves attention and gives each file dedicated analysis.

#### Pass 2 — Cross-file integration review

Give the integration reviewer the per-file findings and ask it to identify:

- data-flow problems
- API consistency issues
- contract violations
- dependency conflicts
- contradictory findings
- issues that only appear when files interact

The key distinction:

**Local passes find local issues.**

**Integration passes find cross-file issues.**

### 1.5 Confidence-based routing

Confidence can be useful after the review process has been established.

A common routing pattern is:

- High confidence → direct report
- Low confidence → human review

However, confidence must be **calibrated**.

### 1.6 Confidence calibration

A model's self-reported confidence is not automatically accurate.

Calibration means comparing:

- predicted confidence
- actual correctness

using a **labelled validation set**.

For example:

1. Collect labelled findings.
2. Record Claude's confidence.
3. Compare confidence with actual correctness.
4. Determine appropriate thresholds.
5. Route future findings using calibrated thresholds.

The key pattern is:

**Raw confidence → labelled validation → calibration → routing**

### 1.7 Production architecture

A robust production review pipeline can be:

1. **Generation**
2. **Independent per-file review**
3. **Integration review**
4. **Confidence routing**
5. **Calibration loop**

This increases cost, but can be worthwhile when reliability matters.

Examples include:

- CI/CD
- financial extraction
- compliance
- production-critical workflows

## Exam Traps

| Wrong answer / approach | Why it is wrong | Correct concept |
|---|---|---|
| Ask Claude in the same session to review its answer | It retains the original reasoning context | Use a separate model instance |
| Put all files into one large prompt | Attention can be diluted | Use per-file passes then integration |
| Assume a larger context window solves review quality | More context does not guarantee more focused attention | Use focused per-file analysis |
| Trust raw confidence scores | Confidence may be poorly calibrated | Calibrate against labelled validation data |
| Use only per-file review | Cross-file problems can be missed | Add an integration pass |

## Practice Scenario

### Scenario

A pull request contains **14 files**. A single-pass review produces inconsistent findings and misses issues in files located in the middle of the context.

### Question

What architecture should you use?

### Correct Answer

**Perform focused per-file local analysis first, followed by a separate cross-file integration review.**

### Explanation

The per-file pass reduces attention dilution and gives each file dedicated analysis. The integration pass then checks issues that require understanding relationships between files.

## Build Exercise

1. Run a baseline single-pass review on a **10-file mock PR**.
2. Measure missed or contradictory findings.
3. Run independent per-file local analysis.
4. Feed the per-file findings into a cross-file integration review.
5. Add confidence scores.
6. Route low-confidence findings to human review.
7. Use an independent Claude instance to review a subset.
8. Compare predicted confidence with actual correctness.
9. Calibrate the confidence thresholds.

## Decision Framework

When designing a review architecture:

1. **Does the model need to challenge an earlier model decision?**
   - Use a separate instance/fresh context.
2. **Are many files being reviewed?**
   - Use per-file local analysis.
3. **Do issues depend on relationships between files?**
   - Add an integration pass.
4. **Are you using confidence for routing?**
   - Calibrate it against labelled validation data.
5. **Does the workflow justify extra cost?**
   - Use the multi-pass architecture for high-reliability workflows.

## Golden Rules ⭐

- **Same-session self-review is not independent review.**
- **Fresh instance = fresh judgement.**
- **Large multi-file prompts can suffer attention dilution.**
- **Per-file analysis improves local coverage.**
- **Integration review finds cross-file issues.**
- **A larger context window does not automatically solve attention problems.**
- **Raw confidence must be calibrated before being used for routing.**
- **High-reliability systems may justify the additional cost of multiple passes.**

## What to Memorise for the Exam

- Same session → retains original reasoning context.
- Independent instance → fresh review context.
- Large multi-file review → attention dilution risk.
- Pass 1 → per-file local analysis.
- Pass 2 → cross-file integration.
- Confidence routing:
  - high confidence → direct report
  - low confidence → human review
- Calibration:
  **confidence + labelled validation → calibrated threshold**
- Production pattern:
  **generation → independent review → integration → confidence routing → calibration**

### Final memory

**Use a fresh instance for independent judgement, analyse files locally first, then integrate cross-file findings and calibrate confidence before routing.**
