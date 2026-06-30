# Step 9: Knowledge Base Structure Template

Generate knowledge base directory structure and initial files.

---

## Directory Structure

    knowledge-base/
    ├── INDEX.md
    ├── contexts/
    │   ├── active/
    │   └── archived/
    ├── prompts/
    │   ├── pm/
    │   ├── frontend/
    │   ├── backend/
    │   ├── qa/
    │   ├── devops/
    │   └── acceptance/
    └── decisions/

Note: Only create subdirectories under prompts/ for user-selected roles.

---

## File 1: INDEX.md

Target file: `knowledge-base/INDEX.md`

```markdown
# Knowledge Base Index

## Active Contexts

_No active tasks_

## Archived Contexts

_No archived tasks_

## Prompt Templates

> Use /knowledge-sync to add your project prompt templates

## Architecture Decisions

> Use /knowledge-sync to record important architecture decisions
```

---

## File 2: ADR Template

Target file: `knowledge-base/decisions/ADR-001-template.md`

```markdown
---
id: ADR-001
status: proposed | accepted | deprecated
date: {date}
decision-makers: {participating roles}
---

# ADR-001: {Title}

## Context
{Why this decision needs to be made}

## Decision
{What was decided}

## Consequences
{Impact of this decision}
```

---

## File 3: Context Template Example

Reference format for /context-handoff generation:

```markdown
---
task-id: {YYYY-MM-DD}-{short-title}
role: {role ID}
status: completed | in-progress | blocked
created: {creation time}
updated: {update time}
related: []
---

## Summary
{2-3 sentences describing what was accomplished}

## Decisions
- {Decision}: {Rationale}

## Changes
- {File path}: {Change content and reason}

## Open Items
- [ ] {Todo}: {Description}

## Key References
- {File or knowledge base link}: {Why it is important}

## Next Steps
- {Suggested next action}
- {Which role to hand off to}
```
