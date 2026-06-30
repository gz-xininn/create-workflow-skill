# Step 3: CLAUDE.md Template

Target file: `{project-root}/CLAUDE.md`

Dynamically generated based on user-selected roles and skills. Use `{placeholder}` for content that needs replacement.

---

## Generated Content

# {project-name}

Multi-role team collaboration workflow environment based on Claude Code native capabilities.

## Project Overview

**Project Name:** {project-name}
**Source Root:** {project-root}

> Please fill in your project information here (tech stack, modules, ports, etc.), preferably in table format:
>
> | Project | Type | Tech Stack | Port |
> |---------|------|-----------|------|
> | {example} | {type} | {tech stack} | {port} |

### Version Locks (Immutable)

> Record immutable dependency versions and their reasons here.

| Project | Dependency | Locked Version | Reason |
|---------|-----------|---------------|--------|
| {project} | {dependency} | {version} | {lock reason} |

---

## Role Routing

At the start of each session, if the user has not declared a role, you must ask first:

> Please select your role:
> {Dynamically generate option list based on roles selected in Step 1}

After confirming the role, read the corresponding context file `docs/rules/roles/{role}.md` and strictly adhere to the role's permission boundaries.
See `ROLES.md` for the full role reference table.

## Task Workflow (Mandatory)

Every task must follow the workflow below. No steps may be skipped:

1. **Semantic Understanding** — Perform semantic analysis on the user's natural language input, output a "Semantic Understanding Confirmation" template (including iteration info and task list), and wait for the user to confirm the understanding is correct. Hallucination and fabrication are forbidden. If unable to understand, prompt directly: `Did not understand, please rephrase your prompt`
2. **Task Initialization** — After semantic understanding is confirmed, use `/task-init` to generate a structured task confirmation sheet. Wait for user confirmation before starting execution
3. **Execution Phase** — Execute strictly within the confirmed scope, following guardrail rules
4. **Task Handoff** — Use `/context-handoff` to compress context and store it in `knowledge-base/contexts/active/`

---

## Security Rules (Absolutely forbidden, cannot be overridden)

* All speculation and hallucination is forbidden; every action must be evidence-based

### Red Zone — Database Security
- Connecting to production databases is forbidden; only local/test databases are allowed
- Production databases must not allow external connections; only local server access is permitted
- Data structures, usernames, and passwords must not match production environments
- DELETE / DROP / TRUNCATE are forbidden; soft deletes must be used (update delete_time)
- Bulk UPDATE must include a WHERE clause and a preceding SELECT confirmation
- Generating migrations directly is forbidden; the design must be presented for user approval first
- After migration completion, the corresponding module documentation must be updated
- For any database-related task, **the database overview document must be read first**
- Real connection strings, passwords, and credentials must never be output

### Red Zone — Git Security
- Force-push to main/master/release branches is forbidden
- Using --no-verify to skip pre-commit hooks is forbidden
- git reset --hard is forbidden (unless the user explicitly requests it)
- Amending already-pushed commits is forbidden; new commits must be created
- Deleting remote branches is forbidden (unless the user explicitly requests it)
- Conventional Commits format is required: `type(scope): description`
- Sensitive information in commit messages is forbidden

### Red Zone — Data Security
- Outputting production environment secrets, API Keys, Tokens, or certificates is forbidden
- Exposing real customer PII (name/phone/ID number/address) is forbidden
- Transmitting core algorithms or business logic to external services is forbidden
- Exposing production URLs, IP addresses, or port mappings in conversations is forbidden
- Outputting .env file contents to conversations or committing them to Git is forbidden
- Code snippets shared with AI must be sanitized (remove real keys, connection strings, PII)
- Sensitive keys must not be transmitted externally; usage requires authorized consent

### Red Zone — Execution Security
- Using `--dangerously-skip-permissions` or similar automation flags to bypass permission confirmation is forbidden
- Destructive operations (rm -rf, del /s /q, DROP TABLE, etc.) are forbidden unless the user explicitly confirms
- Installing packages or modifying dependencies is forbidden unless the user approves
- Changes affecting more than 3 files must first list the file manifest and receive confirmation
- Every task must have a clear scope; unauthorized scope expansion is forbidden
- Sensitive operations (database writes, file deletion, remote push) must be confirmed with the user before execution

### Green Zone — Permitted Operations
- Reading any project source code for analysis
- Sanitized pseudocode and general algorithm discussions
- Error log and stack trace analysis with sensitive information removed
- General utility code, regular expressions, SQL transformations
- Generating documentation, comments, and test scaffolding
- Reading and writing the knowledge-base/ directory
- Generating prompt templates and context summaries
- Git read-only operations: status, log, diff, branch

### Coding Security Constraints
- Avoid hardcoding sensitive information in code; use environment variables for configuration
- Validate user input to prevent SQL injection and XSS attacks
- Implement appropriate access controls

---

## Development Constraints

> Add project-specific development constraints here, for example:
> - Unit testing rules
> - Frontend build rules
> - API call rules

---

## Knowledge Base

Before starting a task, read `knowledge-base/INDEX.md` to check if there is relevant context.
If found, load the corresponding context file directly instead of asking the user to re-explain.
This is the core mechanism for saving tokens.

## Context Loading Order

1. This file (always loaded)
2. Guardrail rules `.claude/rules/guardrails/` (always loaded, core security rules)
3. Role context `docs/rules/roles/{role}.md` (loaded on demand, Read after role is confirmed)
4. Task context `knowledge-base/contexts/active/{task-id}.md` (loaded on demand, if exists)
5. Workflow specs `docs/rules/workflows/` (loaded on demand, Read when cross-role collaboration is involved)

> **Architecture Note**: Role files and workflow files are placed in `docs/rules/` rather than `.claude/rules/` because all files under `.claude/rules/` are automatically loaded by Claude Code as system context. If everything were placed in `.claude/rules/`, each API call would carry too many system prompt tokens, potentially triggering API errors during long conversations. Only the 3 core guardrail files are auto-loaded; the rest are loaded on demand via Read.

## Available Skills

| Skill | Trigger | Purpose |
|-------|---------|---------|
| /task-init | new task, start, begin | Generate task prompt template |
| /context-handoff | save context, handoff, wrap up | Compress and save context |
| /knowledge-sync | sync knowledge base, add template | Knowledge base CRUD |
| /acceptance-check | acceptance, check completion | Acceptance criteria check |
| /security-gate | security check | Security red-line review |
| /generate-help | generate help docs | Generate help.html |

---

## Dynamic Generation Notes

- Role routing option list: only include user-selected roles
- Available skills table: only include user-selected skills
- Security rules: always included in full, cannot be trimmed
