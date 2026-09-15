# Domain 3 — Claude Code Configuration & Workflows

## Domain 3 — Task 3.1: CLAUDE.md Hierarchy, Scoping, and Modular Organisation

Here are **simple, exam-focused notes** for the page you shared. I’ll keep the same structure and level of detail as your Domain 1 and Domain 2 notes.

## 1. What You Need to Know

Claude Code reads configuration from CLAUDE.md files at three levels:

| Level | Location | Purpose |
|---|---|---|
| User | `~/.claude/CLAUDE.md` | Personal preferences |
| Project | `.claude/CLAUDE.md` or root `CLAUDE.md` | Team-wide standards |
| Directory | `subdirectory/CLAUDE.md` | Package/area-specific rules |

### Easy memory

**User → Project → Directory**

- User = **me**
- Project = **team**
- Directory = **specific area**

---

## 2. The Three-Level CLAUDE.md Hierarchy

### User-Level

```text
~/.claude/CLAUDE.md
```

Used for:

- Personal preferences
- Preferred output style
- Personal shortcuts
- Individual working preferences

It is **not version-controlled** and does not travel with the repository.

### Project-Level

```text
.claude/CLAUDE.md
```

or:

```text
CLAUDE.md
```

at the repository root.

Used for:

- Team naming conventions
- Error-handling patterns
- Testing requirements
- Architecture decisions
- Code-review rules

This is version-controlled and shared with the team.

### Directory-Level

Example:

```text
/packages/api/CLAUDE.md
```

Used for:

- Package-specific conventions
- API-specific rules
- Rules that should apply only inside a particular directory

---

## 3. Easy Hierarchy Memory

Remember:

> **User = Personal**  
> **Project = Team**  
> **Directory = Package**

---

## 4. CLAUDE.md Does NOT Work Like Strict Precedence

This is **very important for the exam**.

CLAUDE.md files are **combined/concatenated into the context**.

They do NOT behave like:

```text
Directory > Project > User
```

where the most specific file automatically wins.

Instead, applicable files are loaded together.

### Exam rule

> **CLAUDE.md = guidance, not deterministic configuration precedence.**

---

## 5. What Happens When Multiple CLAUDE.md Files Load?

Suppose you have:

```text
~/.claude/CLAUDE.md
        ↓
project/CLAUDE.md
        ↓
project/packages/api/CLAUDE.md
```

Claude receives the applicable instructions together.

The loading order becomes more specific as Claude moves towards the working directory.

However:

> **Later does NOT guarantee that the later instruction wins.**

If two instructions conflict, Claude may choose either one.

---

## 6. Loading Order

The general idea is:

```text
Broad scope
    ↓
Project scope
    ↓
Directory scope
    ↓
More specific scope
```

Within the same directory:

```text
CLAUDE.md
    ↓
CLAUDE.local.md
```

But remember:

> **Loading order ≠ strict precedence**

---

## 7. Conflict Between CLAUDE.md Files

Example:

### Project CLAUDE.md

```text
Always use double quotes.
```

### Directory CLAUDE.md

```text
Always use single quotes.
```

You should **not** assume:

```text
Directory wins
```

The correct exam understanding is:

> Conflicting CLAUDE.md instructions do not have guaranteed deterministic precedence.

---

## 8. What Should You Do for Mandatory Rules?

If something **must always happen**, do not depend on CLAUDE.md.

Use:

- `settings.json`
- Hooks

Examples:

- Blocking a dangerous tool
- Required formatter
- Permission policy
- Mandatory security rule
- Mandatory workflow check

### Memory

> **CLAUDE.md = guidance**  
> **settings/hooks = enforcement**

---

## 9. CLAUDE.md vs settings.json

| Feature | CLAUDE.md | settings.json |
|---|---|---|
| Main purpose | Instructions/guidance | Configuration/enforcement |
| Strict precedence | ❌ No | ✅ Yes |
| Conflicts | May resolve arbitrarily | Deterministic precedence |
| Good for | Coding conventions | Mandatory policies |

### settings.json precedence

```text
Managed policy
      ↓
Local
      ↓
Project
      ↓
User
```

Managed policy has the highest precedence.

### Exam trap

If the question asks:

> "Which CLAUDE.md file wins?"

Do **not** answer:

> "The most specific one always wins."

Correct concept:

> No CLAUDE.md file is guaranteed to win a conflict.

---

## 10. Modular CLAUDE.md with `@` Imports

Large CLAUDE.md files can become difficult to maintain.

You can split them into smaller files using `@`.

Example:

```markdown
# .claude/CLAUDE.md

Coding standards:

@./standards/naming-conventions.md
@./standards/error-handling.md
@./standards/testing-requirements.md
```

### Important

There is **no**:

```text
@import
```

keyword.

The syntax is simply:

```text
@<path>
```

---

## 11. Correct `@` Syntax

Correct:

```text
@./standards/naming-conventions.md
```

Incorrect:

```text
@import ./standards/naming-conventions.md
```

### Memory

> **@ + path**

---

## 12. Why Use `@` Imports?

Benefits:

- Smaller source files
- Better organisation
- Easier maintenance
- Less duplication
- Reusable standards
- Package-specific imports

Example:

```text
.claude/
├── CLAUDE.md
└── standards/
    ├── naming.md
    ├── testing.md
    └── error-handling.md
```

---

## 13. Important `@` Import Trap

`@` imports make your **source files** cleaner.

But they do **not automatically reduce context size**.

Why?

Because the imported file is loaded/inlined into the context.

Example:

```text
600-line CLAUDE.md
```

split into:

```text
6 × 100-line files
```

The source is easier to manage, but Claude still sees roughly the same amount of content.

### If you want to reduce per-session context

Use:

```text
.claude/rules/
```

with path-specific rules.

---

## 14. CLAUDE.local.md

`CLAUDE.local.md` is used for personal, project-local instructions.

Example:

```text
project/
├── CLAUDE.md
└── CLAUDE.local.md
```

### CLAUDE.md

Shared team rules.

### CLAUDE.local.md

Your personal rules for that project.

---

## 15. CLAUDE.local.md and Git

`CLAUDE.local.md` is commonly added to `.gitignore`.

Why?

Because it normally contains personal settings.

Example:

```text
CLAUDE.local.md
```

may contain:

- Personal scratch paths
- Temporary debugging notes
- Personal preferences
- Extra explanations

---

## 16. Very Important: local Does NOT Mean "Always Overrides"

This is an important exam trap.

Because `CLAUDE.local.md` is loaded after `CLAUDE.md` at the same level, you might think:

```text
CLAUDE.local.md always wins
```

That is **not guaranteed**.

Remember:

> **Load order ≠ precedence**

If there is a conflict, Claude may choose either instruction.

---

## 17. `.claude/rules/`

The `.claude/rules/` directory is useful for topic-specific rules.

Example:

```text
.claude/rules/
├── testing.md
├── api-conventions.md
└── deployment.md
```

Examples:

### testing.md

```text
Test naming conventions
Assertion patterns
Fixture usage
```

### api-conventions.md

```text
Endpoint naming
Request/response schemas
API conventions
```

### deployment.md

```text
Deployment checklist
Environment configuration
```

---

## 18. Path-Scoped Rules

Rules inside `.claude/rules/` can optionally use YAML frontmatter.

Example:

```yaml
---
paths: ["terraform/**/*"]
---
```

This means the rule applies to matching paths.

This becomes especially useful when conventions apply to a **file type across many directories**.

---

## 19. Why `.claude/rules/` Is Powerful

Suppose your test files are spread across:

```text
src/a/test.ts
src/b/test.ts
src/c/test.ts
packages/x/test.ts
packages/y/test.ts
```

You do not want to create many directory-level CLAUDE.md files.

Instead:

```text
.claude/rules/testing.md
```

with:

```yaml
---
paths: ["**/*.test.ts"]
---
```

This loads the testing conventions only when relevant.

### Benefit

> **Less irrelevant context**

---

## 20. Directory CLAUDE.md vs `.claude/rules/`

| Requirement | Best choice |
|---|---|
| Universal team rules | Root `CLAUDE.md` |
| One package only | Directory `CLAUDE.md` |
| File type across many directories | `.claude/rules/` |
| Task-specific workflow | Skills |

### Memory

> **Package → CLAUDE.md**  
> **File type → `.claude/rules/`**

---

## 21. `/memory` and `/context`

These commands are used to diagnose configuration.

They do **not** activate configuration.

### `/memory`

Exam guide expects:

```text
/memory
```

to inspect memory/configuration files.

### `/context`

Current Claude Code can show what actually loaded under:

```text
Memory files
```

---

## 22. `/memory` vs `/context`

| Command | Purpose |
|---|---|
| `/memory` | Exam-keyed command for checking memory files |
| `/context` | Current command for checking what actually loaded |

### Critical rule

> Neither command loads configuration.

Configuration loads automatically based on location.

---

## 23. What Happens During `/compact`?

When you run:

```text
/compact
```

Claude summarises the conversation.

Project-root CLAUDE.md can come back because Claude re-reads it from disk.

---

## 24. What May Not Return Immediately After Compaction?

Two important cases:

### 1. Nested CLAUDE.md

Example:

```text
/packages/api/CLAUDE.md
```

It may reload when Claude reads a matching file.

### 2. Path-scoped `.claude/rules/`

These also load on demand.

So after compaction:

```text
Root CLAUDE.md
        ↓
available again

Nested CLAUDE.md
        ↓
may load when relevant path is accessed

Path-scoped rule
        ↓
may load when matching file is accessed
```

---

## 25. Critical Exam Scenario — New Team Member

Scenario:

Developer A has been working for months.

Claude follows:

- API naming conventions
- Test structure
- Error handling

Developer B joins the team.

Developer B clones the same repository.

But Claude does not follow the team's conventions.

### What is the likely problem?

The conventions are probably stored in:

```text
~/.claude/CLAUDE.md
```

on Developer A's machine.

That configuration is:

- Personal
- Not version-controlled
- Not shared through Git

Developer B therefore never receives it.

---

## 26. Find the Root Cause

### Wrong

```text
~/.claude/CLAUDE.md
```

for team-wide rules.

### Correct

```text
.claude/CLAUDE.md
```

or:

```text
CLAUDE.md
```

at repository root.

---

## 27. Correct Fix

Move team-wide instructions from:

```text
~/.claude/CLAUDE.md
```

to:

```text
project/.claude/CLAUDE.md
```

or:

```text
project/CLAUDE.md
```

### Memory

> **New teammate missing rules → Check user-level config first**

---

## 28. New Team Member Scenario — Easy Memory

```text
Developer A
    ↓
~/.claude/CLAUDE.md
    ↓
Works for A

Developer B
    ↓
Clones repository
    ↓
Does NOT get ~/.claude/CLAUDE.md
```

Therefore:

```text
Team rules → Project-level CLAUDE.md
```

---

## 29. Exam Traps

### Trap 1

**New teammate doesn't receive instructions**

Answer:

> Check whether team conventions are incorrectly stored in `~/.claude/CLAUDE.md`.

---

### Trap 2

**Thinking `/memory` loads configuration**

Wrong.

`/memory` diagnoses configuration.

It does not activate it.

---

### Trap 3

**Assuming the most specific CLAUDE.md always wins**

Wrong.

CLAUDE.md files are combined.

Conflicts have no guaranteed winner.

---

### Trap 4

**Using directory CLAUDE.md for cross-directory file conventions**

Wrong.

Use:

```text
.claude/rules/
```

with path patterns.

---

## 30. Practice Scenario

### Question

Developer A's Claude Code follows the team's API naming conventions perfectly.

Developer B, who joined last week, gets inconsistent naming.

Both work on the same repo and branch.

What is the most likely root cause?

### Correct answer

**Option C**

The API naming conventions are stored in Developer A's:

```text
~/.claude/CLAUDE.md
```

instead of the project-level configuration.

### Why?

Because user-level configuration is not shared through Git.

---

## 31. Build Exercise — What You Should Understand

The build exercise covers:

1. Create project-level `.claude/CLAUDE.md`
2. Create directory-level `CLAUDE.md`
3. Create `.claude/rules/testing.md`
4. Use `@` imports
5. Run `/context`
6. Test user-level vs project-level scoping

The important exam concepts are:

- User vs project vs directory scope
- `@` imports
- `.claude/rules/`
- `/memory`
- `/context`
- New-team-member configuration problems

---

## 32. Complete Decision Framework

Use this table in the exam:

| Requirement | Use |
|---|---|
| Personal preference | `~/.claude/CLAUDE.md` |
| Team-wide rule | Project `CLAUDE.md` |
| Package-specific rule | Directory `CLAUDE.md` |
| Personal project-specific rule | `CLAUDE.local.md` |
| File-type-specific rule | `.claude/rules/` |
| Reusable standards | `@` import |
| Mandatory enforcement | `settings.json` / Hooks |
| Task-specific workflow | Skill |

---

## 33. Complete Hierarchy Diagram

```text
User
└── ~/.claude/CLAUDE.md
        ↓
        Personal

Project
└── CLAUDE.md
    or
    .claude/CLAUDE.md
        ↓
        Team-wide

Directory
└── packages/api/CLAUDE.md
        ↓
        Package-specific

Project-local
└── CLAUDE.local.md
        ↓
        Personal project-specific

Rules
└── .claude/rules/
        ↓
        Topic/file/path-specific
```

---

## 34. CLAUDE.md vs `.claude/rules/`

### CLAUDE.md

Use when the rule should be broadly available.

### `.claude/rules/`

Use when the rule should load conditionally.

Example:

```text
All developers → CLAUDE.md
Only Terraform files → .claude/rules/terraform.md
Only tests → .claude/rules/testing.md
```

---

## 35. CLAUDE.md vs CLAUDE.local.md

| | CLAUDE.md | CLAUDE.local.md |
|---|---|---|
| Purpose | Shared rules | Personal project rules |
| Git | Usually committed | Usually ignored |
| Scope | Team | Individual |
| Example | Naming convention | Personal scratch path |

---

## 36. One-Minute Revision

### User-level

```text
~/.claude/CLAUDE.md
```

Personal.

### Project-level

```text
.claude/CLAUDE.md
```

Team.

### Directory-level

```text
packages/api/CLAUDE.md
```

Package-specific.

### CLAUDE.local.md

Personal project-specific rules.

### `.claude/rules/`

Topic/path-specific rules.

### `@`

Imports another file.

### `/memory`

Exam answer for checking memory/configuration.

### `/context`

Current command to inspect what actually loaded.

### Mandatory rules

Use:

```text
settings.json
hooks
```

---

## 37. Golden Rules ⭐

1. **User-level = personal**
2. **Project-level = team**
3. **Directory-level = package-specific**
4. **CLAUDE.md files are concatenated**
5. **CLAUDE.md does not have strict precedence**
6. **More specific does not guarantee a winner**
7. **Mandatory enforcement → settings.json/hooks**
8. **`@./path.md` is the import syntax**
9. **`@` imports improve organisation but do not reduce context size**
10. **`.claude/rules/` = topic-specific/path-scoped rules**
11. **CLAUDE.local.md = personal project-local rules**
12. **`/memory` diagnoses; it does not activate**
13. **Current `/context` shows what loaded**
14. **New teammate missing conventions → check user-level config**

---

## 38. What to Memorise for the Exam

Memorise these 10:

1. `~/.claude/CLAUDE.md` = personal
2. Root `.claude/CLAUDE.md` or `CLAUDE.md` = project/team
3. Subdirectory `CLAUDE.md` = package-level
4. CLAUDE.md files are combined, not strict override
5. Conflicts have no guaranteed winner
6. `@./path.md` = import
7. `.claude/rules/` = modular/path-scoped rules
8. `CLAUDE.local.md` = personal project-local
9. `/memory` = exam-keyed; `/context` = current actual loaded context
10. New teammate missing conventions → check user-level config

### Final memory sentence

> **Personal rules go in user-level CLAUDE.md, team rules go in project-level CLAUDE.md, package rules go in directory-level CLAUDE.md, reusable/path-specific rules go in `.claude/rules/`, `@` imports organise files, and deterministic requirements belong in settings or hooks—not CLAUDE.md.**

---

# Domain 3 — Task 3.2: Custom Slash Commands and Skills

## 1. What You Need to Know

Custom slash commands and Skills are now part of the same Skills system.

The exam focuses on:

- Where commands/skills live
- Project vs user scope
- `context: fork`
- `allowed-tools`
- `argument-hint`
- When to use CLAUDE.md vs Skills
- Automatic skill invocation
- Exam traps

---

## 2. Custom Commands and Skills Are Unified

The canonical location for Skills is:

```text
.claude/skills/
```

The older commands location is:

```text
.claude/commands/
```

`commands` remains a backward-compatible alias.

### Example

```text
.claude/commands/deploy.md
```

creates:

```text
/deploy
```

A Skill can be:

```text
.claude/skills/deploy/SKILL.md
```

which also creates:

```text
/deploy
```

---

## 3. Important Skill Structure

Correct:

```text
.claude/skills/deploy/SKILL.md
```

Incorrect:

```text
.claude/skills/deploy.md
```

A loose `.md` file directly inside `.claude/skills/` does **not** create a command.

### Memory

> **Skill = directory + SKILL.md**

---

## 4. Why Skills Are More Powerful

Skills provide:

- Automatic discovery
- Intent-based invocation
- Supporting files
- Task-specific workflows
- Optional isolated context

Example:

```text
.claude/skills/
└── deploy/
    ├── SKILL.md
    ├── checklist.md
    └── deployment-guide.md
```

The Skill can reference the supporting files when needed.

---

## 5. Project vs User Scope

### Project scope

```text
.claude/skills/
.claude/commands/
```

These are shared with the team through Git.

### User scope

```text
~/.claude/skills/
~/.claude/commands/
```

These are personal.

### Memory

> **Project = team**  
> **User = personal**

---

## 6. Skill vs Command Precedence

If a Skill and command have the same name, the Skill takes precedence.

Example:

```text
.claude/commands/review.md
```

and:

```text
.claude/skills/review/SKILL.md
```

The Skill version wins.

---

## 7. `context: fork`

Example:

```yaml
---
context: fork
---
```

This runs the Skill in an isolated sub-agent context.

### Why use it?

Useful for:

- Large codebase analysis
- Brainstorming
- Noisy exploration
- Verbose research
- Tasks where you don't want the detailed working process filling the main conversation

### Memory

> **fork = isolated context**

---

## 8. `allowed-tools`

Example:

```yaml
---
allowed-tools:
  - Read
  - Grep
---
```

The exam guide treats this as a way to restrict the tools available to the Skill.

### Important live behaviour

Current Claude Code uses `allowed-tools` to pre-approve listed tools; it is not the same as removing every other tool. For actual security restrictions, use:

- `disallowed-tools`
- Permission deny rules

### Exam answer

If the exam asks:

> What does `allowed-tools` do?

Answer:

> It restricts/pre-approves the tools the Skill can use according to the Skill configuration.

---

## 9. `argument-hint`

Example:

```yaml
---
argument-hint: [branch-name]
---
```

This provides an autocomplete hint when invoking the Skill.

### Important distinction

The current Claude Code behaviour treats it as an **autocomplete hint**, not an interactive parameter prompt.

For exam questions based on the guide, remember the guide's intended concept: it communicates expected arguments.

---

## 10. `description`

A Skill can include:

```yaml
---
description: Review code for security issues
---
```

The description helps Claude decide when the Skill is relevant.

If the user asks something matching the description, Claude may automatically invoke the Skill.

### Memory

> **description = helps intent matching**

---

## 11. Other Useful Skill Fields

You may also encounter:

```text
disallowed-tools
disable-model-invocation
model
effort
when_to_use
```

### `disable-model-invocation`

If:

```yaml
disable-model-invocation: true
```

the Skill requires explicit invocation.

Example:

```text
/release
```

instead of automatic selection.

---

## 12. How Skills Are Invoked

A Skill can be invoked:

### Explicitly

```text
/review
```

### Automatically

Claude can select it based on:

- Description
- User intent
- Relevant paths

Unless automatic invocation is disabled.

---

## 13. Skills vs CLAUDE.md

This distinction is extremely important.

### CLAUDE.md

Used for:

> **Always-loaded standards**

Examples:

- Coding style
- Architecture rules
- Naming conventions
- Team-wide conventions

### Skills

Used for:

> **Task-specific workflows**

Examples:

- Deployment
- Code review
- Database migration
- Release preparation
- Security audit

### Memory

> **Always needed → CLAUDE.md**  
> **Task-specific → Skill**

---

## 14. Do Not Put Task Workflows in CLAUDE.md

Bad design:

```text
CLAUDE.md

When deploying:
1. Run tests
2. Build application
3. Check environment
4. Deploy
5. Verify deployment
```

If deployment is only needed occasionally, this consumes context unnecessarily.

Better:

```text
.claude/skills/deploy/SKILL.md
```

---

## 15. Do Not Put Always-On Standards in Skills

If every session needs:

```text
Use our naming convention.
```

Do not hide that rule inside a Skill.

Use:

```text
CLAUDE.md
```

---

## 16. Path-Specific Rules vs Skills

Use `.claude/rules/` when the rule depends on the **file/path**.

Use Skills when the rule depends on the **task/workflow**.

Example:

| Requirement | Best choice |
|---|---|
| All TypeScript files use strict conventions | `.claude/rules/` |
| Review a pull request | Skill |
| Terraform files use specific formatting | `.claude/rules/` |
| Deploy application | Skill |
| Team naming convention | CLAUDE.md |

---

## 17. Personal Skills

If you want a personal Skill:

```text
~/.claude/skills/brainstorm/SKILL.md
```

Do not put it in the project if teammates should not receive it.

---

## 18. Personal Variants

Suppose the team already has:

```text
/review
```

You want a personal review workflow.

Do not create another identical team-visible Skill.

Use a different personal name:

```text
~/.claude/skills/deep-review/SKILL.md
```

This avoids confusion and does not affect teammates.

---

## 19. Exam Traps

### Trap 1

A file is placed here:

```text
.claude/skills/review.md
```

Does it automatically create a Skill?

**No.**

Use:

```text
.claude/skills/review/SKILL.md
```

---

### Trap 2

Team command is stored here:

```text
~/.claude/commands/
```

Wrong.

That is personal scope.

Team commands should be:

```text
.claude/commands/
```

or:

```text
.claude/skills/
```

---

### Trap 3

Skills are always loaded.

Wrong.

Skills are task-specific and can be loaded on demand.

---

### Trap 4

`context: fork` means "use another model".

Wrong.

It means:

> Use isolated sub-agent context.

---

### Trap 5

Put task-specific procedures in CLAUDE.md.

Usually wrong.

Use a Skill.

---

## 20. Practice Scenario

### Scenario

A team needs:

1. A shared `/review` command.
2. A personal `/brainstorm` workflow.
3. The brainstorming workflow should not flood the main conversation with verbose analysis.

### Correct answer

Use:

```text
.claude/commands/review.md
```

for the team review command.

Use:

```text
~/.claude/skills/brainstorm/SKILL.md
```

with:

```yaml
context: fork
```

for the personal brainstorming workflow.

### Correct option

**Option C**

---

## 21. Build Exercise

You should understand how to:

1. Create a project `/review` Skill
2. Create a personal brainstorming Skill
3. Add `context: fork`
4. Configure `allowed-tools`
5. Configure `argument-hint`
6. Verify project vs user scope
7. Verify isolated context

---

## 22. Quick Reference

| Requirement | Solution |
|---|---|
| Team-wide Skill | `.claude/skills/<name>/SKILL.md` |
| Team-wide command | `.claude/commands/<name>.md` |
| Personal Skill | `~/.claude/skills/<name>/SKILL.md` |
| Personal command | `~/.claude/commands/<name>.md` |
| Universal standards | `CLAUDE.md` |
| Personal preferences | `~/.claude/CLAUDE.md` |
| Isolated execution | `context: fork` |
| Restrict/pre-approve tools | `allowed-tools` |
| Prevent model invocation | `disable-model-invocation: true` |
| Path-specific conventions | `.claude/rules/` |

---

## 23. Golden Rules ⭐

1. **Skills live in `.claude/skills/<name>/SKILL.md`**
2. **`.claude/commands/` is the backward-compatible command location**
3. **Loose `.md` inside `.claude/skills/` does not create a Skill**
4. **Project scope = team**
5. **User scope = personal**
6. **Skill takes precedence over same-name command**
7. **`context: fork` = isolated context**
8. **`allowed-tools` = tool configuration/restriction**
9. **`argument-hint` = expected argument/autocomplete hint**
10. **`description` helps automatic Skill selection**
11. **Task workflow → Skill**
12. **Always-on standard → CLAUDE.md**
13. **Path/file-specific convention → `.claude/rules/`**
14. **`disable-model-invocation: true` = explicit invocation required**

---

## 24. What to Memorise for the Exam

Memorise:

```text
.claude/skills/name/SKILL.md
```

= Skill

```text
.claude/commands/name.md
```

= command

```text
~/.claude/skills/
```

= personal Skill

```text
context: fork
```

= isolated context

```text
CLAUDE.md
```

= always-loaded standards

```text
.claude/rules/
```

= path-specific rules

---

# Domain 3 — Task 3.3: Path-Specific Rules for Conditional Convention Loading

## 1. What You Need to Know

Path-specific rules allow Claude Code to apply conventions **only when working with matching files**.

They are stored in:

```text
.claude/rules/
```

and can use YAML frontmatter such as:

```yaml
---
paths: ["terraform/**/*"]
---
```

---

## 2. What Are Path-Specific Rules?

Path-specific rules are conditional rules.

Instead of loading a rule every time Claude starts, the rule loads when Claude is working with a matching path.

Example:

```yaml
---
paths: ["**/*.test.ts"]
---
```

This rule applies to TypeScript test files.

---

## 3. Where Do They Live?

Use:

```text
.claude/rules/
```

Example:

```text
.claude/rules/
├── testing.md
├── api.md
└── terraform.md
```

---

## 4. YAML Frontmatter

Example:

```markdown
---
paths: ["terraform/**/*"]
---

# Terraform Conventions

Use approved Terraform modules.
Validate configuration before committing.
```

The `paths` field defines when the rule applies.

---

## 5. Glob Patterns

Glob patterns can match across the codebase.

Example:

```yaml
paths: ["**/*.test.tsx"]
```

Matches:

```text
src/a/Button.test.tsx
src/b/Form.test.tsx
packages/ui/Button.test.tsx
```

Another example:

```yaml
paths: ["**/*.test.ts"]
```

---

## 6. Why Not Use Root CLAUDE.md?

Suppose you have testing conventions.

If you put them in:

```text
CLAUDE.md
```

they load for every session.

Even when you are editing:

```text
README.md
```

or:

```text
terraform/main.tf
```

you still carry the testing rules.

This creates unnecessary context.

---

## 7. Why Not Use Directory CLAUDE.md?

Suppose test files are spread across:

```text
src/
packages/
services/
apps/
```

A directory-level CLAUDE.md only applies to one directory.

You would need many files:

```text
src/CLAUDE.md
packages/CLAUDE.md
services/CLAUDE.md
apps/CLAUDE.md
```

This creates:

- Duplication
- Maintenance overhead
- Configuration drift

---

## 8. Best Solution

Use:

```text
.claude/rules/testing.md
```

with:

```yaml
---
paths:
  - "**/*.test.ts"
  - "**/*.test.tsx"
---
```

Now the rule follows the file type.

---

## 9. Examples

### Testing

```yaml
---
paths: ["**/*.test.ts", "**/*.test.tsx"]
---
```

### API

```yaml
---
paths: ["src/api/**/*"]
---
```

### Terraform

```yaml
---
paths: ["terraform/**/*"]
---
```

---

## 10. Path-Specific Rules vs CLAUDE.md

| Requirement | Best solution |
|---|---|
| Universal team standard | Root `CLAUDE.md` |
| One package | Directory `CLAUDE.md` |
| File type across many directories | `.claude/rules/` |
| Task-specific workflow | Skill |

---

## 11. Exam Favourite Scenario

### Scenario

A company has test files distributed across 50+ directories.

They want the same testing conventions applied to all:

```text
*.test.ts
*.test.tsx
```

### Wrong

Create 50 directory-level CLAUDE.md files.

### Wrong

Put all testing rules in root CLAUDE.md.

### Correct

Create:

```text
.claude/rules/testing.md
```

with:

```yaml
---
paths:
  - "**/*.test.ts"
  - "**/*.test.tsx"
---
```

### Answer

**Option B**

---

## 12. Important Exam Trap

### Question

Should you use a Skill for path-specific conventions?

Usually:

**No.**

Skills are for:

> Tasks/workflows

Path-specific rules are for:

> File/path conventions

---

## 13. Another Important Trap

### Question

Should you use a directory-level CLAUDE.md for conventions across many directories?

Usually:

**No.**

Use:

```text
.claude/rules/
```

with glob patterns.

---

## 14. Token Efficiency

Path-specific rules help reduce irrelevant context.

Example:

```text
Testing rules
```

only load when relevant test files are being worked on.

This is more context-efficient than putting all testing rules in root CLAUDE.md.

---

## 15. Practice Scenario

### Question

A team has React tests spread across many directories.

They want:

```text
*.test.ts
*.test.tsx
```

to follow the same testing conventions.

What is the best solution?

### Correct answer

Create:

```text
.claude/rules/testing.md
```

with:

```yaml
---
paths:
  - "**/*.test.ts"
  - "**/*.test.tsx"
---
```

---

## 16. Build Exercise

You should practise:

1. Create a testing rule.
2. Create an API rule.
3. Create a Terraform rule.
4. Add path globs.
5. Use `/context` to verify loaded rules.
6. Switch between different file types.
7. Observe which rules become relevant.

---

## 17. Decision Framework

```text
Is the rule universal?
        ↓
      Yes
        ↓
   CLAUDE.md

Is it package-specific?
        ↓
      Yes
        ↓
Directory CLAUDE.md

Is it file/path-specific across many directories?
        ↓
      Yes
        ↓
.claude/rules/

Is it a task/workflow?
        ↓
      Yes
        ↓
     Skill
```

---

## 18. Golden Rules ⭐

1. **Path-specific rules live in `.claude/rules/`**
2. **Use YAML `paths:` frontmatter**
3. **Use glob patterns**
4. **Rules load conditionally**
5. **File type across many directories → path-specific rule**
6. **One package → directory CLAUDE.md**
7. **Universal standard → root CLAUDE.md**
8. **Task workflow → Skill**
9. **Path-specific rules reduce irrelevant context**
10. **Do not create many duplicate directory CLAUDE.md files for the same file type**

---

## 19. What to Memorise for the Exam

```text
.claude/rules/
```

+

```yaml
paths:
```

+

glob pattern

=

**Conditional convention loading**

### Final memory

> **If the rule follows the file, use `.claude/rules/`; if the rule follows the task, use a Skill.**

---

# Domain 3 — Task 3.4: Plan Mode vs Direct Execution

## 1. What You Need to Know

Claude Code can either:

- Plan before making changes
- Execute directly

The exam tests **when to choose each mode**.

The key concept is:

> **The decision is about ambiguity, not simply difficulty.**

---

## 2. Plan Mode

Use Plan Mode when you need to:

- Explore a codebase
- Understand architecture
- Compare multiple approaches
- Make architectural decisions
- Change multiple files
- Handle an uncertain implementation

Plan Mode allows Claude to investigate and design before modifying files.

---

## 3. Direct Execution

Use direct execution when:

- The task is well understood
- The solution is clear
- The change is limited
- The location is known
- The implementation approach is known

Example:

```text
Change timeout from 30 seconds to 60 seconds.
```

If the location is known, direct execution is appropriate.

---

## 4. Difficulty vs Ambiguity

This is one of the most important exam concepts.

### Difficult but clear

Example:

```text
Fix this single-function bug.
```

It may be technically difficult.

But if:

- The bug is understood
- The fix is obvious
- Only one function is affected

→ **Direct execution**

### Simple-sounding but ambiguous

Example:

```text
Add a caching layer to the application.
```

This may involve:

- Architecture
- Multiple modules
- Several valid approaches
- Trade-offs

→ **Plan Mode**

---

## 5. Easy Memory

> **Known → Direct**  
> **Unknown → Plan**

---

## 6. Explore Subagent

For complex exploration, an Explore subagent can investigate the codebase.

The purpose is to keep verbose exploration output separate from the main conversation.

It can return:

- Findings
- Relevant files
- Architecture information
- Potential approaches

---

## 7. Hybrid Approach

A very common pattern is:

```text
Plan
  ↓
Explore
  ↓
Design
  ↓
Review plan
  ↓
Execute
```

This is often the best approach for complex changes.

---

## 8. Example — Library Migration

Suppose you need to migrate a library across a large codebase.

### Plan phase

Identify:

- Current imports
- API differences
- Affected modules
- Migration pattern
- Edge cases

### Execute phase

Apply the known migration pattern file by file.

### Memory

> **Plan first → execute after the approach is known**

---

## 9. Decision Table

| Situation | Best choice |
|---|---|
| Architectural restructuring | Plan |
| Library migration across many files | Plan → Direct |
| Multiple valid approaches | Plan |
| Codebase exploration | Plan |
| Single-file clear bug | Direct |
| One-function validation check | Direct |
| Config value update | Direct |
| Known fix and location | Direct |

---

## 10. Exam Traps

### Trap 1

"Complex = always Plan."

Not necessarily.

A technically complex but well-understood change can be executed directly.

---

### Trap 2

"Simple = always Direct."

Wrong.

A simple-looking feature can contain architectural ambiguity.

---

### Trap 3

Forgetting the hybrid approach.

Many real tasks are:

```text
Plan → Execute
```

---

### Trap 4

Starting direct and only planning after many changes.

For uncertain multi-file work, planning first reduces unnecessary modifications.

---

## 11. Practice Scenario

Suppose you have three tasks:

1. Restructure authentication across several modules with multiple possible architectures.
2. Change one configuration value.
3. Fix a known bug in one function.

### Correct approach

- Task 1 → **Plan**
- Task 2 → **Direct**
- Task 3 → **Direct**

### Answer

**Option A**

---

## 12. Build Exercise

Practise:

1. Start a Plan Mode session.
2. Explore a multi-file change.
3. Identify affected files.
4. Produce a plan.
5. Switch to direct execution.
6. Apply the approved plan.

---

## 13. Golden Rules ⭐

1. **Plan Mode = uncertainty/architecture/exploration**
2. **Direct execution = clear, bounded task**
3. **Decision is about ambiguity, not difficulty**
4. **Multiple approaches → Plan**
5. **Known fix → Direct**
6. **Multi-file migration → Plan then Direct**
7. **Explore subagent isolates verbose discovery**
8. **Hybrid Plan → Execute is common**

---

## 14. What to Memorise for the Exam

```text
Unknown / ambiguous
        ↓
      PLAN

Known / clear
        ↓
     DIRECT

Large migration
        ↓
PLAN → DIRECT
```

### Final memory

> **If you don't yet know the best way to do it, plan. If you already know exactly what to do, execute directly.**

---

# Domain 3 — Task 3.5: Iterative Refinement Techniques

## 1. What You Need to Know

The first output is often not the final output.

The exam tests which refinement technique is best for the situation.

There are three major techniques:

1. Concrete input/output examples
2. Test-driven iteration
3. Interview pattern

---

## 2. Technique Hierarchy

| Technique | Best for |
|---|---|
| Concrete examples | Inconsistent interpretation |
| Test-driven iteration | Complex transformations |
| Interview pattern | Unfamiliar domains |

---

## 3. Concrete Input/Output Examples

This is often the **most effective technique** when Claude interprets a requirement inconsistently.

Instead of saying:

```text
Format the output correctly.
```

provide examples.

Example:

### Input

```text
customer_id = 123
```

### Expected output

```json
{
  "customerId": 123
}
```

Give approximately:

```text
2–3 examples
```

The model can generalise the transformation.

---

## 4. Why Examples Work

Prose can be interpreted differently.

Examples provide an exact mapping:

```text
Input → Output
```

This reduces ambiguity.

### Memory

> **Show, don't just tell.**

---

## 5. Test-Driven Iteration

Use test-driven refinement when the transformation is complex.

Approach:

```text
Requirement
   ↓
Write tests
   ↓
Implement
   ↓
Run tests
   ↓
Inspect failures
   ↓
Refine
```

Tests can cover:

- Happy path
- Edge cases
- Performance requirements
- Error conditions

---

## 6. Why Test-Driven Iteration Works

A failed test gives concrete feedback.

Instead of:

```text
This is still wrong.
```

you get:

```text
Expected X
Received Y
```

This is much easier to correct.

---

## 7. Interview Pattern

Use the interview pattern when the developer/model does not understand the domain well enough.

Example:

```text
Before implementing the billing workflow, ask me questions about:
- Business rules
- Regulatory requirements
- Existing systems
- Edge cases
```

This surfaces hidden requirements.

---

## 8. Examples vs Interview

This distinction is important.

### Use examples when:

The desired transformation is known.

But Claude is interpreting it inconsistently.

### Use interview when:

The domain itself is unfamiliar.

Important requirements are missing.

### Memory

> **Known output but inconsistent → Examples**  
> **Unknown domain → Interview**

---

## 9. Feedback Batching

Sometimes multiple issues should be fixed together.

### Batch feedback when issues interact.

Example:

- Error code change
- Logging change
- SDK type change

These may affect one another.

---

## 10. Sequential Feedback

Use sequential refinement when issues are independent.

Example:

1. Rename variable
2. Fix indentation

These can be handled independently.

### Memory

> **Interdependent → Batch**  
> **Independent → Sequential**

---

## 11. Example Communication Flow

Suppose Claude produces inconsistent output.

Use:

```text
Observe inconsistency
        ↓
Provide 2–3 concrete examples
        ↓
Verify generalisation
        ↓
Add edge-case examples if needed
```

---

## 12. Decision Table

| Situation | Best technique |
|---|---|
| Prose interpreted differently | Concrete examples |
| Exact transformation known | Concrete examples |
| Complex transformation with many edge cases | Test-driven |
| Unfamiliar domain | Interview |
| Interdependent issues | Batch |
| Independent issues | Sequential |

---

## 13. Exam Traps

### Trap 1

"Use more precise prose."

Not always.

If the model interprets prose differently, provide examples.

---

### Trap 2

"Always batch feedback."

Wrong.

Batch only when issues interact.

---

### Trap 3

"Use interview pattern for formatting."

Usually wrong.

If the exact output is known, use examples.

---

## 14. Practice Scenario

Claude is producing inconsistent output for a transformation.

You know exactly what the correct input/output mapping should be.

What should you do?

### Correct answer

Provide:

> **2–3 concrete input/output examples**

### Answer

**Option A**

---

## 15. Build Exercise

Practise:

1. Give Claude a transformation requirement.
2. Observe inconsistent output.
3. Add 2–3 examples.
4. Test the result.
5. Add edge-case examples.
6. Compare batch vs sequential feedback.

---

## 16. Golden Rules ⭐

1. **Concrete examples → inconsistent interpretation**
2. **2–3 examples are usually enough**
3. **Test-driven → complex transformations**
4. **Interview → unfamiliar domain**
5. **Interdependent issues → batch**
6. **Independent issues → sequential**
7. **Examples provide exact input/output mapping**
8. **Tests provide objective feedback**

---

## 17. What to Memorise for the Exam

```text
Inconsistent interpretation
        ↓
    Examples

Complex transformation
        ↓
   Test-driven

Unknown domain
        ↓
    Interview

Issues interact
        ↓
      Batch

Issues independent
        ↓
   Sequential
```

### Final memory

> **Examples clarify, tests verify, interviews discover.**

---

# Domain 3 — Task 3.6: CI/CD Integration

## 1. What You Need to Know

Claude Code can be integrated into CI/CD pipelines for:

- Code review
- Code generation
- Automated analysis
- Test generation
- Technical debt reports

The exam strongly focuses on:

```text
-p / --print
```

---

## 2. `-p` / `--print`

In CI/CD, Claude must run non-interactively.

Use:

```bash
claude -p "Review this code"
```

or:

```bash
claude --print "Review this code"
```

### Why?

Without print mode, Claude may wait for interactive input.

That can cause the CI job to hang.

### Exam answer

> **Use `-p` / `--print` for non-interactive CI execution.**

---

## 3. Common Wrong Answers

Do not confuse `-p` with:

```text
CLAUDE_HEADLESS=true
```

or:

```text
--batch
```

or:

```text
stdin /dev/null
```

The directly tested option is:

```text
-p
```

---

## 4. `--output-format`

Claude Code supports output formats such as:

```text
text
json
stream-json
```

Example:

```bash
claude -p "Review this code" --output-format json
```

This produces a JSON envelope around the result.

---

## 5. `--json-schema`

You can validate structured output against a JSON Schema.

Example:

```bash
claude -p "Return review findings" \
  --json-schema review-schema.json
```

When used with JSON output, structured schema-conforming data is available in:

```text
structured_output
```

You can extract it with:

```bash
jq '.structured_output'
```

---

## 6. Independent Code Review

Do not ask the same Claude session that generated code to review its own code when you want an independent review.

Why?

The original session already has:

- Previous reasoning
- Design decisions
- Justifications
- Context that may bias the review

### Better approach

Use a separate Claude Code invocation.

```text
Generation session
        ↓
Generated code

Separate review session
        ↓
Independent review
```

### Memory

> **Generate → separate reviewer**

---

## 7. Incremental Review

Suppose a previous review found:

```text
Issue A
Issue B
```

After fixes, do not ask the reviewer to rediscover everything.

Provide previous findings and ask:

> Find only new or still-unaddressed issues.

This reduces duplicate comments.

---

## 8. CLAUDE.md in CI

Claude Code can read project CLAUDE.md in CI.

Therefore, put useful project review information there:

- Testing standards
- Fixtures
- Review criteria
- Existing coverage
- Project conventions

This helps Claude understand the repository.

---

## 9. Important CLI Flags

### System prompt

```text
--system-prompt
```

Replaces the default system prompt.

```text
--system-prompt-file
```

Replaces the default system prompt with a file.

### Append system prompt

```text
--append-system-prompt
```

Adds additional instructions while preserving the default system prompt.

```text
--append-system-prompt-file
```

Adds instructions from a file.

---

## 10. Replace vs Append

### Append

Use when you want Claude to remain a coding assistant but add CI-specific rules.

Example:

```text
Default coding assistant
+
CI review requirements
```

### Replace

Use when the identity/permission model needs to be completely different.

### Memory

> **Same identity + extra rules → Append**  
> **Different identity/permission model → Replace**

---

## 11. Other Important Flags

You may see:

```text
--output-format text|json|stream-json
--input-format text|stream-json
--json-schema
--max-turns
--permission-mode
--allowedTools
--disallowedTools
--tools
--add-dir
--model
-c / --continue
-r / --resume
```

---

## 12. `--bare`

`--bare` skips automatic discovery of:

- Hooks
- Skills
- Plugins
- MCP servers
- Auto memory
- CLAUDE.md

It leaves the basic Bash and file read/edit capabilities.

### Memory

> **--bare = minimal automatic discovery**

---

## 13. Existing Tests in CI

If existing tests already cover an area, include that context.

Otherwise Claude may recommend:

```text
Create tests for X
```

when tests already exist.

This causes duplicate or unnecessary work.

---

## 14. Real-Time vs Batch

### Real-time synchronous

Use for:

- Pre-merge blocking checks
- Immediate CI feedback
- Pull-request gates

### Batch

Use for:

- Overnight technical debt reports
- Weekly audits
- Nightly test generation
- Large asynchronous analysis

---

## 15. Batch API

Batch API can provide:

> **50% cost savings**

But it can take:

> **up to 24 hours**

and does not provide guaranteed low-latency response.

Therefore:

```text
Pre-merge blocking check
        ↓
Real-time

Overnight report
        ↓
Batch
```

---

## 16. Exam Traps

### Trap 1

CI pipeline hangs.

Correct fix:

```text
-p / --print
```

---

### Trap 2

Use same session for generation and review.

Wrong when independent review is required.

Use a separate invocation.

---

### Trap 3

Use Batch API for pre-merge blocking checks.

Wrong.

Blocking checks need real-time results.

---

### Trap 4

Review from scratch every time.

Wrong.

Pass previous findings and ask for new/still-unresolved issues.

---

## 17. Practice Scenario

A CI pipeline runs Claude Code to review pull requests.

The pipeline hangs because Claude waits for interactive input.

What should you add?

### Correct answer

```bash
-p
```

or:

```bash
--print
```

### Answer

**Option C**

---

## 18. Build Exercise

Practise:

1. Run Claude in print mode.
2. Produce JSON output.
3. Add a JSON Schema.
4. Extract `structured_output`.
5. Run an independent review session.
6. Pass previous findings to avoid duplicate review comments.

---

## 19. Decision Framework

```text
Need non-interactive CI?
        ↓
     -p / --print

Need machine-readable output?
        ↓
   --output-format json

Need schema validation?
        ↓
    --json-schema

Need independent review?
        ↓
 Separate invocation

Need blocking pre-merge check?
        ↓
 Real-time

Need overnight/weekly analysis?
        ↓
 Batch
```

---

## 20. Golden Rules ⭐

1. **CI/CD → use `-p` / `--print`**
2. **`--output-format json` → JSON envelope**
3. **`--json-schema` → validate structured output**
4. **Schema output → `structured_output`**
5. **Use separate invocation for independent review**
6. **Pass previous findings for incremental review**
7. **CLAUDE.md is available in CI**
8. **Append system prompt when keeping default identity**
9. **Replace system prompt when identity/permission model differs**
10. **`--bare` skips automatic discovery**
11. **Pre-merge blocking → real-time**
12. **Overnight/weekly analysis → Batch**
13. **Batch can save 50% cost but may take up to 24 hours**

---

## 21. What to Memorise for the Exam

### CI execution

```text
claude -p "..."
```

### JSON

```text
--output-format json
```

### Schema

```text
--json-schema
```

### Structured output

```text
.structured_output
```

### Review

```text
Generation session
        ↓
Separate review session
```

### Timing

```text
Blocking → Real-time
Reports → Batch
```

### Final memory

> **For CI, use `-p`; for machine-readable results use JSON; for validated structured output use `--json-schema`; for independent review use a separate invocation; and use real-time for blocking checks but Batch for non-urgent reports.**
