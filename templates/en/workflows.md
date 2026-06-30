# Step 7: Workflow File Templates

Generate 3 files to the `docs/rules/workflows/` directory.

---

## File 1: feature-dev.md

Target file: `docs/rules/workflows/feature-dev.md`

# Feature Development Workflow

## Workflow Overview

    Product Manager → Frontend/Backend Dev → QA/Tester → DevOps → Acceptance
         (PM)              (FE/BE)            (QA)       (OPS)     (ACC)

## Phase Details

### Phase 1: Requirements Definition (Product Manager)
1. Run /task-init, select role PM, task type Feature
2. Write requirements document (PRD) using knowledge-base/prompts/pm/prd-template.md
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

---

## File 2: hotfix.md

Target file: `docs/rules/workflows/hotfix.md`

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

---

## File 3: release.md

Target file: `docs/rules/workflows/release.md`

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
