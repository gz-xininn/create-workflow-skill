# Step 6: Guardrail Rule File Templates

Generate 3 files to the `.claude/rules/guardrails/` directory.

---

## File 1: boundaries.md

Target file: `.claude/rules/guardrails/boundaries.md`

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

---

## File 2: git-workflow.md

Target file: `.claude/rules/guardrails/git-workflow.md`

# Git Workflow Standards

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

---

## File 3: redlines.md

Target file: `.claude/rules/guardrails/redlines.md`

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
| DB-07 | Production databases must not allow external connections; data structures and credentials must not match production | Immediate termination |

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
| EXEC-01 | Using `--dangerously-skip-permissions` or similar automation flags to bypass permission confirmation is forbidden | Immediate termination |
| EXEC-02 | Destructive commands like rm -rf / del /s /q are forbidden (unless the user confirms) | Refuse execution |
| EXEC-03 | Installing/uninstalling packages or modifying dependencies is forbidden (unless the user approves) | Await approval |
| EXEC-04 | Changes affecting more than 3 files must first list the file manifest and receive confirmation | Confirm before executing |
| EXEC-05 | Launching browsers/build processes or modifying CI/CD configuration is forbidden (unless the user explicitly requests it) | Await instruction |
| EXEC-06 | Task scope must not be expanded without authorization; re-run /task-init if expansion is needed | Rollback to task-init |
