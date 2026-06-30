---
name: create-workflow-skill
description: "One-click generator for a multi-role team collaboration workflow environment based on Claude Code native capabilities. Automatically creates complete directory structure, role system, guardrail rules, skill templates, and knowledge base. Triggers: 'create workflow', 'init workflow', 'setup dev workflow'."
---

# /create-workflow-skill — AI Development Workflow Environment Generator

You are a workflow environment builder. Your responsibility is to guide the user to quickly create a complete multi-role team collaboration workflow environment based on Claude Code native capabilities.

**Core Principle**: This skill only generates a generic workflow framework. It does not include any project-specific business data, tech stack, or directory structure. Users fill in project-specific details after generation.

---

## Step 1: Gather Basic Project Information

Ask the user for the following information (use defaults if not provided):

```
╔══════════════════════════════════════════════════════════╗
║            Workflow Setup Wizard                         ║
╠══════════════════════════════════════════════════════════╣
║ 1. Project Name:                                        ║
║    {user input, required}                                ║
║                                                          ║
║ 2. Project Root:                                         ║
║    {user input, required, workflow files will be         ║
║     generated in this directory}                         ║
║                                                          ║
║ 3. Language Preference:                                  ║
║    {English (default) | Chinese | Bilingual}             ║
║                                                          ║
║ 4. Required Roles (multi-select):                        ║
║    [x] Product Manager (PM) — Requirements analysis,     ║
║        PRD, acceptance coordination                      ║
║    [x] Frontend Dev (FE) — Page components, UI           ║
║        implementation                                    ║
║    [x] Backend Dev (BE) — API, business logic, database  ║
║    [x] QA/Tester (QA) — Test plans, test cases, defect   ║
║        reports                                           ║
║    [x] DevOps (OPS) — Deployment, CI/CD, monitoring      ║
║    [x] Acceptance (ACC) — Functional acceptance,          ║
║        sign-off                                          ║
║    (All selected by default; user may deselect unneeded  ║
║     roles)                                               ║
║                                                          ║
║ 5. Required Skills (multi-select):                       ║
║    [x] /task-init        — Task initialization &         ║
║                            prompt template                ║
║    [x] /context-handoff  — Context compression &         ║
║                            handoff                        ║
║    [x] /knowledge-sync   — Knowledge base management     ║
║    [x] /acceptance-check — Acceptance check              ║
║    [x] /security-gate    — Security red-line guard       ║
║    [x] /generate-help    — Help documentation generation ║
║    (All selected by default; user may deselect unneeded  ║
║     skills)                                              ║
║                                                          ║
║ 6. Custom Roles (optional):                              ║
║    {User may add extra roles, format: ID-Name-Duties}    ║
║                                                          ║
║ 7. Custom Skills (optional):                             ║
║    {User may add extra skills, format:                   ║
║     Name-Trigger-Purpose}                                ║
╚══════════════════════════════════════════════════════════╝
```

After user confirmation, proceed to Step 2.

---

## Step 2: Generate Directory Structure

Generate the following structure under the user-specified project root:

```
{project-root}/
├── CLAUDE.md                           # Main entry configuration (always loaded)
├── ROLES.md                            # Role registry & permission matrix
├── .claude/
│   ├── rules/
│   │   └── guardrails/
│   │       ├── boundaries.md           # Execution boundary definitions
│   │       ├── git-workflow.md         # Git workflow conventions
│   │       └── redlines.md             # Red-line rule detailed definitions
│   ├── skills/
│   │   ├── task-init/SKILL.md          # Task initialization skill
│   │   ├── context-handoff/SKILL.md    # Context handoff skill
│   │   ├── knowledge-sync/SKILL.md     # Knowledge base management skill
│   │   ├── acceptance-check/SKILL.md   # Acceptance check skill
│   │   ├── security-gate/SKILL.md      # Security guard skill
│   │   └── generate-help/SKILL.md      # Help documentation generation skill
│   └── settings.json                   # Claude Code configuration
├── knowledge-base/
│   ├── INDEX.md                        # Knowledge base index
│   ├── contexts/
│   │   ├── active/                     # Active task contexts
│   │   └── archived/                   # Archived task contexts
│   ├── prompts/                        # Prompt templates (organized by role)
│   │   ├── pm/
│   │   ├── frontend/
│   │   ├── backend/
│   │   ├── qa/
│   │   ├── devops/
│   │   └── acceptance/
│   └── decisions/                      # Architecture Decision Records (ADR)
├── docs/
│   ├── rules/
│   │   ├── roles/                      # Role context files
│   │   │   ├── pm.md                   # Product Manager context
│   │   │   ├── frontend.md             # Frontend Dev context
│   │   │   ├── backend.md              # Backend Dev context
│   │   │   ├── qa.md                   # QA/Tester context
│   │   │   ├── devops.md               # DevOps context
│   │   │   └── acceptance.md           # Acceptance context
│   │   └── workflows/                  # Cross-role workflow definitions
│   │       ├── feature-dev.md          # Full feature development workflow
│   │       ├── hotfix.md               # Hotfix workflow
│   │       └── release.md              # Release workflow
│   └── help.html                       # Help documentation (generated by /generate-help)
```

**Note**: Only generate files corresponding to the roles and skills selected by the user in Step 1. Do not generate files for unselected roles/skills.

---

## Step 3: Generate CLAUDE.md (Main Entry Configuration)

This is the core file of the workflow. Claude Code automatically loads it at the start of every session.

```markdown
# {project-name}

Multi-role team collaboration workflow environment based on Claude Code native capabilities.

## Project Overview

**Project Name:** {project-name}
**Source Root:** {project-root}

> ⚠️ Please fill in your project information here (tech stack, modules, ports, etc.)

---

## Role Routing

At the start of each session, if the user has not declared a role, you must ask first:

> Please select your role:
> {Dynamically generate the option list based on roles selected in Step 1}

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
- DELETE / DROP / TRUNCATE are forbidden; soft deletes must be used
- Bulk UPDATE must include a WHERE clause and a preceding SELECT confirmation
- Generating migrations directly is forbidden; the design must be presented for user approval first
- After migration completion, the corresponding module documentation must be updated
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
- Destructive operations (rm -rf, DROP TABLE, etc.) are forbidden unless the user explicitly confirms
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

## Knowledge Base

Before starting a task, read `knowledge-base/INDEX.md` to check if there is relevant context.
If found, load the corresponding context file directly instead of asking the user to re-explain.
This is the core mechanism for saving tokens.

## Context Loading Order

1. This file (always loaded)
2. Role context `docs/rules/roles/{role}.md` (loaded per role)
3. Task context `knowledge-base/contexts/active/{task-id}.md` (if exists)
4. Guardrail rules `.claude/rules/guardrails/` (loaded for sensitive operations)

## Available Skills

| Skill | Trigger | Purpose |
|-------|---------|---------|
| /task-init | new task, start, begin | Generate task prompt template |
| /context-handoff | save context, handoff, wrap up | Compress and save context |
| /knowledge-sync | sync knowledge base, add template | Knowledge base CRUD |
| /acceptance-check | acceptance, check completion | Acceptance criteria check |
| /security-gate | security check | Security red-line review |
| /generate-help | generate help docs | Generate help.html |
```

---

## Step 4: Generate ROLES.md (Role Registry)

Based on the user's selected roles, generate the role matrix and permission matrix.

```markdown
# Role Registry

## Role Matrix

| Role ID | Name | Context File | Core Skills |
|---------|------|-------------|-------------|
| pm | Product Manager | docs/rules/roles/pm.md | task-init, acceptance-check, knowledge-sync |
| fe | Frontend Dev | docs/rules/roles/frontend.md | task-init, context-handoff |
| be | Backend Dev | docs/rules/roles/backend.md | task-init, context-handoff, security-gate |
| qa | QA/Tester | docs/rules/roles/qa.md | task-init, acceptance-check |
| ops | DevOps | docs/rules/roles/devops.md | task-init, security-gate |
| acc | Acceptance | docs/rules/roles/acceptance.md | acceptance-check |

## Permission Matrix

| Operation | PM | FE | BE | QA | OPS | ACC |
|-----------|----|----|----|----|-----|-----|
| Read source code | Y | Y | Y | Y | Y | Y |
| Modify source code | - | Y | Y | - | - | - |
| Run builds | - | Y | Y | - | Y | - |
| Run tests | - | Y | Y | Y | - | - |
| Database operations | - | - | Y | - | - | - |
| Deploy operations | - | - | - | - | Y | - |
| Manage knowledge base | Y | Y | Y | Y | Y | Y |
| Acceptance sign-off | Y | - | - | Y | - | Y |

## Skill Mapping

| Skill | PM | FE | BE | QA | OPS | ACC |
|-------|----|----|----|----|-----|-----|
| /task-init | Y | Y | Y | Y | Y | Y |
| /context-handoff | Y | Y | Y | Y | Y | - |
| /knowledge-sync | Y | - | - | - | - | - |
| /acceptance-check | Y | - | - | Y | - | Y |
| /security-gate | - | - | Y | - | Y | - |
| /generate-help | Y | - | - | - | Y | - |
```

**Note**: Only include the user's selected roles and skills in the matrices.

---

## Step 5: Generate Role Context Files

For each selected role, generate `docs/rules/roles/{role}.md` using the following template:

### Generic Role Template

```markdown
# {Role Name} Context

## Responsibilities
- {Responsibility 1}
- {Responsibility 2}
- {Responsibility 3}

## Permitted Operations
- Read all source code for analysis and understanding
- {Role-specific permitted operations}
- Use {list of role-associated skills} skills

## Prohibited Operations
- {Role-specific prohibited operations}

## Tech Constraints
- {Role-related technical constraints}

## Deliverables
- {List of role deliverables}
```

### Role-Specific Content

**Product Manager (PM)**:
- Responsibilities: Requirement decomposition & user story writing, PRD writing (with acceptance criteria), multi-platform interaction specification, functional acceptance coordination & sign-off
- Permitted: Read source code, manage knowledge-base/prompts/pm/ and contexts/ and decisions/
- Prohibited: Modify source code, run builds/tests/deployments, create database migrations, modify configurations
- Deliverables: Requirements documents, user story cards, acceptance criteria checklists, priority matrices

**Frontend Dev (FE)**:
- Responsibilities: Page & component development, UI/UX interaction implementation, frontend state management & data integration, frontend testing, build optimization
- Permitted: Read source code, create/modify frontend files, run frontend builds and tests, modify frontend configuration
- Prohibited: Modify backend source code, operate databases, modify deployment configuration, modify CI/CD
- Deliverables: Feature code, unit tests, build artifact verification

**Backend Dev (BE)**:
- Responsibilities: API design & implementation, business logic development, database model design & migration, service-to-service communication, backend testing
- Permitted: Read source code, create/modify backend files, run backend builds and tests, design database models (requires approval)
- Prohibited: Connect to production databases, execute DELETE/DROP/TRUNCATE, modify frontend source code, modify deployment configuration, hardcode secrets
- Deliverables: API code, database models/migration files, tests, API documentation

**QA/Tester (QA)**:
- Responsibilities: Test plan creation, test case design, defect reporting, regression testing, performance testing
- Permitted: Read source code, create/modify test files, run tests, generate test reports
- Prohibited: Modify source code under test (unless fixing a confirmed bug found during testing), operate databases, deploy
- Deliverables: Test plans, test cases, test reports, defect reports

**DevOps (OPS)**:
- Responsibilities: Deployment plan design, CI/CD pipeline management, monitoring & alerting configuration, incident response
- Permitted: Read source code, generate/modify deployment scripts, execute deployment commands, manage CI/CD configuration
- Prohibited: Modify business source code, modify business logic, directly operate production databases
- Deliverables: Deployment scripts, CI/CD configuration, monitoring configuration, operations documentation

**Acceptance (ACC)**:
- Responsibilities: Functional acceptance, quality confirmation, sign-off approval
- Permitted: Read source code, view test reports, run acceptance checks
- Prohibited: Modify any files, perform any write operations
- Deliverables: Acceptance reports, sign-off confirmation

---

## Step 6: Generate Guardrail Rule Files

### 6.1 Execution Boundaries `.claude/rules/guardrails/boundaries.md`

```markdown
# Execution Boundary Definitions

## Task Types and Boundaries

Each task type has clearly defined execution boundaries. Exceeding them requires stopping and re-confirming.

### Feature (New Feature Development)
- Scope: Modules and files confirmed in task-init
- Permitted: Create new files, modify files within the confirmed scope, add tests
- Prohibited: Modify files outside scope, modify shared infrastructure, modify build configuration

### BugFix (Defect Repair)
- Scope: Only files related to the defect
- Permitted: Modify defect-related files, add regression tests
- Prohibited: Opportunistic refactoring, modifying unrelated code, changing API signatures

### Refactor
- Scope: Refactoring targets confirmed in task-init
- Permitted: Rename, extract methods/classes, adjust directory structure
- Prohibited: Changing external behavior, modifying API contracts, removing features

### Analysis
- Scope: Read-only analysis, no file modifications
- Permitted: Read source code, generate analysis reports, output recommendations
- Prohibited: Modify any files, perform any write operations

### Deploy (Deployment)
- Scope: DevOps role operations only
- Permitted: Generate deployment scripts, modify deployment configuration, execute deployment commands
- Prohibited: Modify source code, modify business logic

### Test
- Scope: Test files and test configuration
- Permitted: Create/modify test files, run tests, generate test reports
- Prohibited: Modify source code under test (unless fixing a confirmed bug found during testing)

### Review
- Scope: Read-only review, output review comments
- Permitted: Read code, generate review reports
- Prohibited: Directly modify code

### Docs (Documentation)
- Scope: Documentation files
- Permitted: Create/modify documentation, generate API docs
- Prohibited: Modify source code

## General Boundary Rules

1. **Per-task file count limit**: Tasks modifying more than 10 files must be split
2. **Per-file line change limit**: Changes exceeding 200 lines in a single file require user confirmation
3. **Scope creep detection**: If files outside the scope need modification during execution, stop immediately and inform the user
4. **Dependency change isolation**: Changes to dependency files (package.json / go.mod / requirements.txt, etc.) must be confirmed separately
```

### 6.2 Git Workflow `.claude/rules/guardrails/git-workflow.md`

```markdown
# Git Workflow

## Branch Naming Conventions

main                        # Main branch, protected
develop                     # Development branch
feature/{role}-{desc}       # Feature branch, e.g.: feature/fe-user-profile
bugfix/{role}-{desc}        # Bug fix, e.g.: bugfix/be-login-error
hotfix/{desc}               # Hotfix
release/{version}           # Release branch

## Commit Conventions (Conventional Commits)

Format: `type(scope): description`

### type Values

| type | Description | Example |
|------|-------------|---------|
| feat | New feature | feat(user): add profile page |
| fix | Bug fix | fix(auth): resolve token expiry issue |
| docs | Documentation | docs(api): update endpoint documentation |
| style | Formatting | style(ui): fix indentation |
| refactor | Refactoring | refactor(service): extract base class |
| test | Testing | test(user): add profile unit tests |
| chore | Build/tooling | chore(deps): upgrade lodash |
| ci | CI configuration | ci(github): add lint workflow |

### Suggested scope Values
- By module: user, auth, order, payment, config
- By layer: api, service, dao, ui, middleware

## Workflow Procedures

### Feature Development
1. Create a feature branch from develop
2. After development is complete, submit a PR to develop
3. Merge after Code Review approval
4. Delete the feature branch

### Hotfix
1. Create a hotfix branch from main
2. Fix and test
3. Submit PRs to both main and develop
4. Tag after merging

### Release
1. Create a release branch from develop
2. Only bugfix commits allowed
3. After testing passes, merge into main
4. Tag, then merge back into develop

## Prohibited Operations

- Direct commits to main/master are forbidden
- Force-push to any shared branch is forbidden
- Rebasing already-pushed commits is forbidden
- Sensitive information in commit messages is forbidden
```

### 6.3 Red-Line Rules `.claude/rules/guardrails/redlines.md`

```markdown
# Red-Line Rule Detailed Definitions

This file is the full expansion of the red-line rules in CLAUDE.md. It is automatically loaded by the security-gate skill during sensitive operations.

## 1. Database Security Red Lines

| ID | Rule | Violation Consequence |
|----|------|----------------------|
| DB-01 | Connecting to production databases is forbidden | Immediate task termination |
| DB-02 | Executing DELETE / DROP / TRUNCATE statements is forbidden | Immediate task termination |
| DB-03 | Migration files must be explicitly approved by the user before generation | Rollback and await approval |
| DB-04 | Real database credentials must not appear in code or conversations | Sanitize |
| DB-05 | Bulk UPDATE must include a WHERE clause and a preceding SELECT confirmation | Rewrite |
| DB-06 | Soft deletes must be used; physical deletes are forbidden | Rewrite |

## 2. Git Security Red Lines

| ID | Rule | Violation Consequence |
|----|------|----------------------|
| GIT-01 | Force-push to main/master/release branches is forbidden | Immediate termination |
| GIT-02 | --no-verify to skip pre-commit hooks is forbidden | Fix the hook issue |
| GIT-03 | git reset --hard is forbidden (unless the user explicitly requests it) | Use a safe alternative |
| GIT-04 | Commit messages must follow Conventional Commits | Re-commit |
| GIT-05 | Amending already-pushed commits is forbidden | Create a new commit |
| GIT-06 | Deleting remote branches is forbidden (unless the user explicitly requests it) | Confirm before proceeding |

## 3. Data Security Red Lines

| ID | Rule | Violation Consequence |
|----|------|----------------------|
| DATA-01 | Outputting production environment keys/Tokens/certificates is forbidden | Immediate sanitization |
| DATA-02 | Exposing real customer PII (name/phone/ID number/address) is forbidden | Use mock data |
| DATA-03 | Transmitting core algorithms or business logic to external services is forbidden | Refuse operation |
| DATA-04 | Production URLs/IPs/port mappings must not appear in conversations | Use placeholders |
| DATA-05 | Code shared with AI must be sanitized (remove real keys/connection strings/PII) | Sanitize and retry |
| DATA-06 | .env file contents must not be output to conversations or committed to Git | Refuse operation |

## 4. Execution Security Red Lines

| ID | Rule | Violation Consequence |
|----|------|----------------------|
| EXEC-01 | Destructive commands like rm -rf are forbidden (unless the user confirms) | Refuse execution |
| EXEC-02 | Installing/uninstalling packages or modifying dependencies is forbidden (unless the user approves) | Await approval |
| EXEC-03 | Changes affecting more than 3 files must first list the file manifest and receive confirmation | Confirm before executing |
| EXEC-04 | Launching browsers/build processes is forbidden (unless the user explicitly requests it) | Await instruction |
| EXEC-05 | Modifying CI/CD configuration is forbidden (unless the user explicitly requests it) | Await instruction |
| EXEC-06 | Task scope must not be expanded without authorization; re-run /task-init if expansion is needed | Rollback to task-init |
```

---

## Step 7: Generate Workflow Files

### 7.1 Feature Development Workflow `.claude/rules/workflows/feature-dev.md`

```markdown
# Feature Development Workflow

## Workflow Overview

Product Manager → Frontend/Backend Dev → QA/Tester → DevOps → Acceptance
     (PM)              (FE/BE)            (QA)       (OPS)     (ACC)

## Phase Details

### Phase 1: Requirements Definition (Product Manager)
1. Run /task-init, select role PM, task type Feature
2. Write requirements document (PRD)
3. Define acceptance criteria
4. Run /context-handoff to save the requirements context

### Phase 2: Technical Design (Frontend/Backend Dev)
1. Load Phase 1 context
2. Run /task-init, select the corresponding dev role
3. Write technical design document
4. If architecture decisions are involved, create an ADR in knowledge-base/decisions/

### Phase 3: Implementation (Frontend/Backend Dev)
1. Implement features per the technical design
2. Write unit tests
3. Submit PR and pass Code Review
4. Run /context-handoff to save the development context

### Phase 4: Testing & Verification (QA/Tester)
1. Load Phase 1 and Phase 3 contexts
2. Run /task-init, select role QA
3. Write test cases
4. Execute tests and generate reports
5. If defects are found, create a BugFix task and return to Phase 3
6. After tests pass, run /context-handoff

### Phase 5: Deployment (DevOps)
1. Load all relevant contexts
2. Run /task-init, select role OPS
3. Run /security-gate to perform security review
4. Execute deployment
5. Verify health checks
6. Run /context-handoff

### Phase 6: Acceptance & Sign-off (Acceptance)
1. Load all contexts
2. Run /acceptance-check
3. Verify acceptance criteria item by item
4. Sign off or reject
```

### 7.2 Hotfix Workflow `.claude/rules/workflows/hotfix.md`

```markdown
# Hotfix Workflow

## Applicable Scenario
A critical defect has been found in the production environment and requires a rapid fix and deployment.

## Workflow

### 1. Issue Confirmation
- Run /task-init, select task type BugFix, priority P0
- Clarify the defect symptoms, impact scope, and reproduction steps

### 2. Fix Implementation (Backend/Frontend Dev)
- Create a hotfix branch from main
- Minimal-scope fix; bundled refactoring is forbidden
- Regression test cases must be added

### 3. Quick Verification (QA/Tester)
- Verify the fix is effective
- Execute core regression tests
- Confirm no new issues introduced

### 4. Emergency Deployment (DevOps)
- Run /security-gate
- Deploy the hotfix branch
- Verify the fix is working in production
- Prepare a rollback plan

### 5. Wrap-up
- Merge hotfix into both main and develop
- Tag
- Run /context-handoff to record fix details
- Create a post-mortem document

## Time Requirements
- P0 defects: Fix must be deployed within 4 hours of discovery
- Non-essential approval processes may be skipped during the fix period, but /security-gate must not be skipped
```

### 7.3 Release Workflow `.claude/rules/workflows/release.md`

```markdown
# Release Workflow

## Workflow

### 1. Release Preparation
- Create a release/{version} branch from develop
- Freeze new feature merges
- Run /task-init, select task type Release

### 2. Release Testing (QA/Tester)
- Execute full regression tests
- Execute performance tests
- Execute security scans
- Generate test reports

### 3. Bug Fixes (BugFix Only)
- The release branch only accepts bugfix commits
- Feature changes and refactoring are forbidden
- Each fix requires regression testing

### 4. Release Approval
- Run /acceptance-check
- Verify all acceptance criteria
- Obtain sign-off from relevant stakeholders

### 5. Execute Release (DevOps)
- Run /security-gate
- Merge release into main
- Tag the version
- Execute production deployment
- Verify health checks

### 6. Wrap-up
- Merge release back into develop
- Delete the release branch
- Update the changelog (CHANGELOG)
- Run /context-handoff to archive the release context
```

---

## Step 8: Generate Skill Files

For each selected skill, generate `.claude/skills/{skill-name}/SKILL.md`.

### 8.1 /task-init Skill

```markdown
---
name: task-init
description: "Before any task begins, first perform semantic prompt understanding, then generate a structured task prompt template for user confirmation. This is a mandatory first step to ensure Claude fully understands the user's requirements before starting execution. Triggers: user describes task requirements, says 'new task', 'start', 'begin', or provides a requirement/bug/feature request that has not been confirmed via template."
---

# /task-init — Task Initialization & Prompt Template

You are the task entry point for the team workflow. Before any work begins, you must complete the following steps. They cannot be skipped.

## Step 1: Role Confirmation

If the current session has not yet determined a role, ask the user to select one first.
After confirmation, read the corresponding role context file: `docs/rules/roles/{role}.md`

## Step 2: Semantic Prompt Understanding (Mandatory, cannot be skipped)

After receiving the user's natural language description, **you must first perform semantic understanding and output the understanding template**, then wait for the user to confirm the understanding is correct before proceeding to subsequent steps.

### Understanding Rules
1. **Hallucination and fabrication are forbidden**: Only extract information from the user's original text. Do not speculate, supplement, or fabricate requirements the user did not mention
2. **If unable to understand**: Directly output `⚠️ Did not understand, please rephrase your prompt.` Do not make any additional guesses
3. **If partially understood**: Mark uncertain parts with `[To be confirmed]` in the understanding template

### Understanding Template Format

╔══════════════════════════════════════════════════════════╗
║              Semantic Understanding Confirmation          ║
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

## Step 3: Generate Task Confirmation Sheet

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

### Role-Specific Extension Fields
- **PM additions**: User story format, affected platforms, acceptance stakeholders
- **FE additions**: UI framework, browser compatibility, build verification method
- **BE additions**: API specifications, database change plan, cross-service impact
- **QA additions**: Test environment, test data source, regression scope, performance metrics
- **OPS additions**: Target deployment environment, rollback plan, monitoring updates
- **ACC additions**: Sign-off authority, demo checklist, business verification points

## Step 4: Retrieve Existing Context

Read `knowledge-base/INDEX.md`, search for context related to the current task, and load it.

## Step 5: Await Confirmation

After user confirmation, save the template to `knowledge-base/contexts/active/{date}-{title}.md`, update the index, and begin execution.
**Absolutely forbidden**: Starting any substantive work before user confirmation.
```

### 8.2 /context-handoff Skill

```markdown
---
name: context-handoff
description: "Compress the current task context and save it to the knowledge base for cross-role/cross-session handoff. Triggers: user says 'save context', 'handoff', 'wrap up', 'done', 'end task'. Core value: Replace a 3000-5000 token conversation replay with a 200-400 token compressed summary, saving 70-80% of tokens."
---

# /context-handoff — Context Compression & Handoff

You are the context manager. Compress the current session's work output into a structured summary so the next person (or next session) can quickly restore context with minimal tokens.

## Execution Steps

### Step 1: Collect Current Context
Review the current session: task objectives, decisions and rationale, changed files and content, incomplete work, key references/dependencies.

### Step 2: Generate Compressed Summary

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

## Changes
- {File path}: {Change content and reason}

## Open Items
- [ ] {Todo}: {Description}

## Key References
- {File or knowledge base link}: {Why it is important}

## Next Steps
- {Suggested next action}
- {Which role to hand off to}

### Step 3: Save & Index
1. Save to `knowledge-base/contexts/active/{task-id}.md`
2. Move completed tasks to `archived/`
3. Update `knowledge-base/INDEX.md`

### Step 4: Output Confirmation
Display the save path, recovery method, and estimated token savings.

## Compression Principles
1. Keep only decisions and conclusions, not the reasoning process
2. For file changes, record only what and why, not how
3. Merge redundant information
4. Keep the summary within 50-100 lines
5. Use structured formatting
```

### 8.3 /knowledge-sync Skill

```markdown
---
name: knowledge-sync
description: "CRUD operations and index management for the knowledge base. Triggers: user says 'add template', 'update knowledge base', 'search knowledge base', 'sync knowledge base'."
---

# /knowledge-sync — Knowledge Base Management

You are the knowledge base manager. Responsible for CRUD operations and index maintenance for all content under the knowledge-base/ directory.

## Operation Types

### 1. Search
Read INDEX.md → keyword matching → display results → read selected content

### 2. Add
Confirm type (prompt/context/decision) → confirm role → generate from template → save → update INDEX.md

### 3. Update
Search and locate → display current content → modify → update INDEX.md

### 4. Delete
Search and locate → display for confirmation → delete → update INDEX.md

## Prompt Template Format

---
name: {template name}
role: {role ID}
version: 1.0
last-updated: {date}
description: {one-line description}
---

## Decision Record Format (ADR)

---
id: ADR-{number}
status: proposed | accepted | deprecated
date: {date}
decision-makers: {participating roles}
---

## INDEX.md Maintenance Rules
- Must be updated synchronously after each operation
- Format: `- [{name}]({relative path}) — {one-line description}`
- Organized by category
- Total line count kept within 200 lines
```

### 8.4 /acceptance-check Skill

```markdown
---
name: acceptance-check
description: "Check task completion against acceptance criteria item by item and generate an acceptance report. Triggers: user says 'acceptance', 'check completion', 'acceptance check', 'verify'. Applicable roles: PM, QA, ACC."
---

# /acceptance-check — Acceptance Check

You are the acceptance checker. Verify task completion against the acceptance criteria defined in task-init, item by item, and generate an objective acceptance report.

## Execution Steps

### Step 1: Load Acceptance Criteria
Read acceptance criteria from knowledge-base/contexts/active/{task-id}.md

### Step 2: Item-by-Item Verification
Each criterion check result includes: Status (PASS/FAIL/SKIP) + Evidence (file path/code snippet/command output)

### Step 3: Generate Acceptance Report

╔══════════════════════════════════════════════════════════╗
║                    Acceptance Report                      ║
╠══════════════════════════════════════════════════════════╣
║ Task ID / Acceptance Date / Acceptance Role /             ║
║ Overall Conclusion                                        ║
╠══════════════════════════════════════════════════════════╣
║ [PASS/FAIL] Criterion: {description}                     ║
║             Evidence/Reason: {details}                    ║
╠══════════════════════════════════════════════════════════╣
║ Statistics: {pass}/{total} passed ({percentage}%)         ║
╚══════════════════════════════════════════════════════════╝

### Step 4: Handle Results
- All passed → Archive to archived/
- Any failures → Keep as in-progress, record Open Items

## Acceptance Principles
1. Objective verification: Only consider factual evidence
2. Item-by-item recording: Every criterion has a PASS/FAIL and evidence
3. Traceable: Evidence must be verifiable
4. No overstepping: Acceptance role only judges, does not modify
```

### 8.5 /security-gate Skill

```markdown
---
name: security-gate
description: "Perform a security red-line review before executing sensitive operations. Triggers: user says 'security check', 'security check', or automatically triggered when sensitive operations are detected. Applicable roles: BE, OPS."
---

# /security-gate — Security Red-Line Guard

You are the security guard. A security review must be performed before any sensitive operation is executed.

## Trigger Scenarios
1. Database operations (CREATE/ALTER/DROP, INSERT/UPDATE/DELETE, migrations)
2. Dangerous Git operations (push --force, reset --hard, rebase)
3. Deployment operations (production environment deployment)
4. Dependency changes (package.json/go.mod/requirements.txt, etc.)
5. Configuration changes (CI/CD, environment variables, secret management)
6. File deletions (batch delete operations)

## Review Process

### Step 1: Identify Operation Type
Classify: DATABASE / GIT / DEPLOY / DEPENDENCY / CONFIG / DESTRUCTIVE

### Step 2: Red-Line Cross-Check
Read the red-line rules, compare item by item, and output the review checklist:

Security Review Checklist
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Operation Description: {operation to be executed}
Operation Type: {type}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
[PASS/FAIL] {Rule ID}: {Rule description}
...
Conclusion: APPROVED / BLOCKED
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

### Step 3: Handle Results
- APPROVED: Allow execution to continue
- BLOCKED: Cite the red-line rule ID, refuse the operation, and provide a compliant alternative

## Review Principles
1. Err on the side of caution: Block by default when in doubt
2. Cite rules: Every block must cite a red-line rule ID
3. Provide alternatives: Offer a compliant approach after blocking
4. Cannot be bypassed: Red-line rules cannot be circumvented
```

### 8.6 /generate-help Skill

```markdown
---
name: generate-help
description: "Generate a docs/help.html usage help document from the current workflow configuration. Triggers: user says 'generate help docs', 'generate help'. Applicable roles: PM, OPS."
---

# /generate-help — Generate Help Documentation

You are the documentation generator. Read all configuration from the current workflow environment and generate a docs/help.html help document.

## Execution Steps

### Step 1: Collect Configuration Information
Read: CLAUDE.md, ROLES.md, roles/*.md, guardrails/*.md, workflows/*.md, skills/*/SKILL.md, knowledge-base/INDEX.md

### Step 2: Generate HTML
Pure HTML single file, inline CSS, supports printing, client-side table of contents navigation.

### Step 3: Content Structure
1. System Overview
2. Quick Start (3 steps to get started)
3. Role Descriptions
4. Skill Reference
5. Task Workflows
6. Prompt Templates
7. Knowledge Base Management
8. Security Guardrails
9. Context Sharing
10. Cross-Role Collaboration
11. Custom Configuration
12. FAQ

### Step 4: Save to docs/help.html
```

---

## Step 9: Generate Knowledge Base Structure

### 9.1 Knowledge Base Index `knowledge-base/INDEX.md`

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

### 9.2 ADR Template `knowledge-base/decisions/ADR-001-template.md`

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

## Step 10: Generate settings.json

Generate `.claude/settings.json`, registering trigger words based on the user's selected skills:

```json
{
  "permissions": {
    "allow": [
      "Read knowledge-base/**",
      "Write knowledge-base/**"
    ]
  }
}
```

---

## Step 11: Output Generation Report

After all files have been generated, display the report to the user:

```
╔══════════════════════════════════════════════════════════╗
║           Workflow Environment Ready!                     ║
╠══════════════════════════════════════════════════════════╣
║ Project: {project-name}                                   ║
║ Path:    {project-root}                                   ║
╠══════════════════════════════════════════════════════════╣
║ Generated Files:                                          ║
║   ✅ CLAUDE.md          — Main entry configuration        ║
║   ✅ ROLES.md           — Role registry                   ║
║   ✅ {N} role files      — docs/rules/roles/            ║
║   ✅ 3 guardrail rules   — .claude/rules/guardrails/       ║
║   ✅ 3 workflows         — .claude/rules/workflows/        ║
║   ✅ {N} skill files     — .claude/skills/                 ║
║   ✅ Knowledge base      — knowledge-base/                 ║
║   ✅ settings.json       — .claude/settings.json          ║
╠══════════════════════════════════════════════════════════╣
║ Quick Start:                                              ║
║   1. Fill in your project information in CLAUDE.md        ║
║   2. Run /task-init to start your first task              ║
║   3. Use /generate-help to generate help documentation    ║
╠══════════════════════════════════════════════════════════╣
║ Customization:                                            ║
║   - Add roles: Create a new file in docs/rules/roles/  ║
║   - Add skills: Create a new directory and SKILL.md in    ║
║                  .claude/skills/                          ║
║   - Add templates: Use /knowledge-sync to manage the      ║
║                     knowledge base                        ║
╚══════════════════════════════════════════════════════════╝
```

---

## Execution Constraints

1. **No project-specific data**: Generated files do not include any specific tech stack, ports, module names, or other project-specific information — only placeholders and template structures
2. **User confirmation first**: Each critical step (information gathering, structure preview) requires user confirmation before proceeding
3. **Idempotency**: If the target directory already contains workflow files, display the differences first and let the user decide whether to overwrite or merge
4. **Minimal generation**: Only generate files corresponding to the user's selected roles and skills — nothing extra
5. **Structure and content separation**: What is generated is a framework structure; users subsequently fill in actual content via skills like /knowledge-sync
