# Step 8a: Core Skill File Templates

Generate 3 skill files. Each skill requires a SKILL.md in its corresponding directory.

---

## Skill 1: /task-init

Target file: `.claude/skills/task-init/SKILL.md`

frontmatter:

    ---
    name: task-init
    description: "Before any task begins, first perform semantic prompt understanding, then generate a structured task prompt template for user confirmation. This is a mandatory first step to ensure Claude fully understands the user's requirements before starting execution. Triggers: user describes task requirements, says 'new task', 'start', 'begin', or provides a requirement/bug/feature request that has not been confirmed via template. Every question must first output a prompt template."
    ---

Content:

# /task-init — Task Initialization & Prompt Template

You are the task entry point for the team workflow. Before any work begins, you must complete the following steps. They cannot be skipped.

## Step 1: Role Confirmation

If the current session has not yet determined a role, ask the user to select one first:

```
Please select your role:
1. Product Manager (PM)  — Requirements analysis, PRD writing, acceptance coordination
2. Frontend Dev (FE)     — Page component development, UI implementation
3. Backend Dev (BE)      — API design, business logic, database
4. QA/Tester (QA)        — Test plans, case design, defect reports
5. DevOps (OPS)          — Deployment scripts, CI/CD, monitoring
6. Acceptance (ACC)      — Functional acceptance, sign-off confirmation
```

After confirmation, read the corresponding role context file: `docs/rules/roles/{role}.md`

## Step 2: Semantic Prompt Understanding (Mandatory, cannot be skipped)

After receiving the user's natural language description, **you must first perform semantic understanding and output the understanding template**, then wait for the user to confirm the understanding is correct before proceeding to subsequent steps.

### Understanding Rules

1. **Hallucination and fabrication are forbidden**: Only extract information from the user's original text. Do not speculate, supplement, or fabricate requirements the user did not mention
2. **If unable to understand**: If the user's description is too vague, contradictory, or no valid task information can be extracted, **output directly**:
   ```
   ⚠️ Did not understand, please rephrase your prompt.
   ```
   Do not make any additional guesses or supplements; wait for the user to re-enter.
3. **If partially understood**: If some content can be understood but some is uncertain, mark uncertain parts with `[To be confirmed]` in the understanding template for the user to supplement

### Understanding Template Format

```
╔══════════════════════════════════════════════════════════╗
║              Prompt Understanding Confirmation            ║
╠══════════════════════════════════════════════════════════╣
║ Iteration:                                                ║
║   Iteration: {extracted from user description,            ║
║              mark [To be confirmed] if not mentioned}     ║
║   Iteration Goal: {the overall goal of this iteration,    ║
║              mark [To be confirmed] if not mentioned}     ║
╠══════════════════════════════════════════════════════════╣
║ Task List:                                                ║
║   1. {specific task extracted from user's original text}  ║
║   2. {specific task extracted from user's original text}  ║
║   ...                                                    ║
╠══════════════════════════════════════════════════════════╣
║ Source Quote:                                             ║
║   "{key statements from user's original input, as basis   ║
║     for understanding}"                                   ║
╠══════════════════════════════════════════════════════════╣
║ Understanding Confidence: {High|Medium|Low}               ║
║ Uncertain Items: {list points needing user clarification, ║
║                   or "None"}                              ║
╚══════════════════════════════════════════════════════════╝

Please confirm whether the above understanding is correct? (Confirm / Correct / Rephrase)
```

### Execution Requirements

- Every task must have a corresponding basis in the user's original text; adding tasks without basis is forbidden
- The "Source Quote" field in the understanding template is used for traceability to ensure no hallucination
- Only after the user confirms the understanding is correct (e.g., replies: "Confirm", "Correct", "OK") proceed to Step 3 to generate the task sheet
- If the user replies "Correct" with supplemental info, re-output the understanding template
- If the user replies "Rephrase", wait for new input

## Step 3: Generate Task Prompt Template

Based on the user's described requirements, fill in the following template and present it to the user for confirmation:

```
╔══════════════════════════════════════════════════════════╗
║                   Task Confirmation Sheet                 ║
╠══════════════════════════════════════════════════════════╣
║ Role            : {current role}                          ║
║ Task Type       : {Feature|BugFix|Refactor|Analysis|     ║
║                    Deploy|Test|Review|Docs}               ║
║ Priority        : {P0-Critical|P1-High|P2-Medium|P3-Low} ║
║ Task Title      : {distilled from user description}       ║
╠══════════════════════════════════════════════════════════╣
║ Scope:                                                    ║
║   Modules Involved: {list involved modules/services}      ║
║   Files Involved:   {list potentially involved files or   ║
║                      directories}                         ║
║   Out of Scope:     {explicitly excluded content}         ║
╠══════════════════════════════════════════════════════════╣
║ Constraints:                                              ║
║   Technical: {framework version, language version, API    ║
║              compatibility, etc.}                         ║
║   Security:  {auto-populated red-line rules based on      ║
║              task type}                                   ║
╠══════════════════════════════════════════════════════════╣
║ Prerequisites:                                            ║
║   Dependent Tasks: {tasks that must be completed first}   ║
║   Existing Context: {related knowledge base context       ║
║                      files}                               ║
╠══════════════════════════════════════════════════════════╣
║ Expected Output:                                          ║
║   Deliverables: {code/docs/config/reports}                ║
╠══════════════════════════════════════════════════════════╣
║ Acceptance Criteria:                                      ║
║   1. {Criterion 1}                                        ║
║   2. {Criterion 2}                                        ║
║   3. {Criterion 3}                                        ║
╚══════════════════════════════════════════════════════════╝
```

### Role-Specific Extension Fields

Auto-append the following fields based on role:

**PM additions**: User story format, affected platforms, acceptance stakeholders
**FE additions**: UI framework, browser compatibility, build verification method
**BE additions**: API specification (RESTful/GraphQL), database change plan, cross-service impact
**QA additions**: Test environment, test data source, regression scope, performance metrics
**OPS additions**: Target deployment environment, rollback plan, monitoring updates
**ACC additions**: Sign-off authority, demo checklist, business verification points

## Step 4: Retrieve Existing Context

Read `knowledge-base/INDEX.md`, search for context related to the current task. If found:
- List related context entries for user confirmation on whether to load
- Load confirmed context files to avoid user having to re-describe

## Step 5: Await Confirmation

After displaying the complete template, wait for explicit user confirmation (e.g., "Confirm", "OK", "Start").

After confirmation:
1. Save the template to `knowledge-base/contexts/active/{date}-{title}.md`
2. Update `knowledge-base/INDEX.md`
3. Begin task execution

**Absolutely forbidden**: Starting any substantive work before user confirmation.

---

## Skill 2: /context-handoff

Target file: `.claude/skills/context-handoff/SKILL.md`

frontmatter:

    ---
    name: context-handoff
    description: "Compress the current task context and save it to the knowledge base, and auto-generate prompt logs to prompt-logs/. Triggers: user says 'save context', 'handoff', 'wrap up', 'done', 'end task', or when a task is completed, or when switching roles to continue the same task. Core value: Replace a 3000-5000 token conversation replay with a 200-400 token compressed summary, saving 70-80% of tokens."
    ---

Content:

# /context-handoff — Context Compression & Handoff

You are the context manager. Your responsibility is to compress the current session's work output into a structured summary so the next person (or next session) can quickly restore context with minimal tokens. Additionally, auto-generate prompt logs to archive all prompt reasoning processes from this session.

## Execution Steps

### Step 1: Collect Current Context

Review all work in the current session and extract:
- What was the task objective
- What decisions were made and why
- What files were changed and what was changed
- What work remains incomplete
- What key references/dependencies exist

### Step 2: Generate Compressed Summary

Generate a summary file in the following format:

```markdown
---
task-id: {YYYY-MM-DD}-{short-title}
role: {current role}
status: completed | in-progress | blocked
created: {creation time}
updated: {current time}
related: [{list of related context IDs}]
---

## Summary
{2-3 sentences describing what was accomplished}

## Decisions
- {Decision 1}: {Rationale}
- {Decision 2}: {Rationale}

## Changes
- {File path}: {Change content and reason}

## Open Items
- [ ] {Todo 1}: {Description}
- [ ] {Todo 2}: {Description}

## Key References
- {File or knowledge base link}: {Why it is important}

## Next Steps
- {Suggested next action}
- {Which role to hand off to}
```

### Step 3: Save & Index

1. Save to `knowledge-base/contexts/active/{task-id}.md`
2. If task is completed, move to `knowledge-base/contexts/archived/`
3. Update the entry in `knowledge-base/INDEX.md`

### Step 4: Generate Prompt Log (Auto-executed, cannot be skipped)

While saving context, automatically generate a prompt log file to archive all prompt reasoning processes from this session.

#### 4.1 Collect Metadata

Auto-collect via the following methods:
- **Developer**: Execute `git config user.name` (convert to lowercase for filename)
- **Date**: Current date, format YYYYMMDD
- **Iteration**: Extract iteration info from the current task's task-init confirmation sheet
- **Role**: The role confirmed in the current session (PM/FE/BE/QA/OPS/ACC mapped to Chinese: 产品/前端/后端/测试/运维/验收)

#### 4.2 File Naming

Format: `{name}-{date}-{iteration}-{role}-{seq}.md`

- `{name}`: git user.name in lowercase, e.g. `zhaoyg`
- `{date}`: YYYYMMDD, e.g. `20260629`
- `{iteration}`: iteration identifier, e.g. `BI1.0.7`
- `{role}`: role name in Chinese, e.g. `前端`
- `{seq}`: two-digit sequence number, starting from `01`

Sequence rule: Scan `knowledge-base/prompt-logs/` directory for files with the same prefix (name-date-iteration-role), take the max sequence number +1. If no files with the same prefix exist, start from `01`.

Example: `zhaoyg-20260629-BI1.0.7-前端-01.md`

#### 4.3 Retroactively Extract All Prompt Reasoning from Session

Review the entire session history and extract each independent prompt reasoning process. One reasoning = one complete "user raises requirement → semantic understanding → execution complete" cycle.

For each reasoning, extract:
1. **User natural language original**: The user's original text describing the requirement
2. **Semantic understanding confirmation**: The corresponding semantic understanding confirmation template (including iteration, task list, confidence, etc.)
3. **Completion report**: Execution result summary of that reasoning (what was done, what files were changed, key decisions)

#### 4.4 Write Prompt Log File

Save to `knowledge-base/prompt-logs/{filename}.md`, format as follows:

```markdown
---
developer: {git user.name original value}
date: {YYYY-MM-DD}
iteration: {iteration identifier}
role: {role name in Chinese}
session-context: {corresponding context filename, e.g. 20260629-bi-filter-adjustment}
record-count: {number of reasoning records in this file}
created: {creation time}
---

# Prompt Log

## Session Info

| Field | Value |
|-------|-------|
| Developer | {developer} |
| Date | {date} |
| Iteration | {iteration} |
| Role | {role} |
| Related Context | {session-context} |

---

## Reasoning Record List

### Record 1

**User Natural Language Original:**

> {user's complete original input text}

**Semantic Understanding Confirmation:**

{complete content of the semantic understanding confirmation template, preserving original format}

**Completion Report:**

{execution result of this reasoning: what was completed, which files were modified, key decisions}

---

### Record 2

**User Natural Language Original:**

> {user's complete original input text}

**Semantic Understanding Confirmation:**

{complete content of the semantic understanding confirmation template}

**Completion Report:**

{execution result of this reasoning}

---

(More records appended in this format...)
```

#### 4.5 Update INDEX.md

Add an entry under the "Prompt Logs" section in `knowledge-base/INDEX.md`:
```
- [{filename}](prompt-logs/{filename}.md) — {iteration} {role} {date} prompt log ({N} records)
```

### Step 5: Output Confirmation

Display the saved summary and prompt log to the user, and inform:
- Context file save path
- Prompt log file save path and record count
- How to restore in the next session (which context file to load)
- Estimated token savings

## Compression Principles

1. **Keep only decisions and conclusions**, not the reasoning process
2. **For file changes, record only what and why**, not how (the code itself is the how)
3. **Merge redundant information**, consolidate multiple changes to the same file into one entry
4. **Keep the summary within 50-100 lines**
5. **Use structured formatting** for easy machine parsing and human reading

---

## Skill 3: /knowledge-sync

Target file: `.claude/skills/knowledge-sync/SKILL.md`

frontmatter:

    ---
    name: knowledge-sync
    description: "CRUD operations and index management for the knowledge base. Triggers: user says 'add template', 'update knowledge base', 'search knowledge base', 'sync knowledge base', or when managing prompt templates, context summaries, or architecture decision records under the knowledge-base/ directory."
    ---

Content:

# /knowledge-sync — Knowledge Base Management

You are the knowledge base manager. Responsible for CRUD operations and index maintenance for all content under the knowledge-base/ directory.

## Operation Types

### 1. Search

When the user needs to find knowledge base content:
1. Read `knowledge-base/INDEX.md`
2. Match relevant entries by keyword
3. Display matching results for user selection
4. Read and display the content of the selected entry

### 2. Add

When the user needs to add new content:
1. Confirm content type: prompt (prompt template) | context (context) | decision (decision record)
2. Confirm the associated role
3. Generate content using the corresponding template format
4. Save to the correct directory:
   - Prompts → `knowledge-base/prompts/{role}/`
   - Contexts → `knowledge-base/contexts/active/`
   - Decisions → `knowledge-base/decisions/`
5. Update `knowledge-base/INDEX.md`

### 3. Update

When the user needs to modify existing content:
1. Search and locate the target file
2. Display current content
3. Modify according to user instructions
4. Update the description in INDEX.md (if the purpose changed)

### 4. Delete

When the user needs to remove outdated content:
1. Search and locate the target file
2. Display content for user confirmation
3. Delete the file after user confirms
4. Remove the corresponding entry from INDEX.md

## Prompt Template Format

New prompt templates must follow this format:

```markdown
---
name: {template name}
role: {role ID: pm|fe|be|qa|ops|acc}
version: 1.0
last-updated: {date}
description: {one-line description, used for INDEX.md}
---

# {Template Title}

## Applicable Scenarios
{When to use this template}

## Template Content
{Actual prompt template with {placeholders}}

## Usage Example
{A filled-in example}
```

## Decision Record Format (ADR)

```markdown
---
id: ADR-{number}
status: proposed | accepted | deprecated
date: {date}
decision-makers: {participating roles}
---

# ADR-{number}: {Title}

## Context
{Why this decision needs to be made}

## Decision
{What was decided}

## Consequences
{Impact of this decision}
```

## INDEX.md Maintenance Rules

- INDEX.md must be updated synchronously after each operation
- Each entry format: `- [{name}]({relative path}) — {one-line description}`
- Organized by category: Active Contexts / Archived Contexts / Prompts (by role) / Decisions
- Total line count kept within 200 lines
