# Step 5: Role Context File Templates

Generate `docs/rules/roles/{role}.md` for each selected role.

---

## Generic Role Template Structure

Each role file contains: Responsibilities, Permitted Operations, Prohibited Operations, Tech Constraints, Deliverables.

---

## Product Manager (PM)

Target file: `docs/rules/roles/pm.md`

# Product Manager Context

## Responsibilities
- Requirement decomposition and user story writing
- PRD writing (with acceptance criteria)
- Multi-platform interaction specification (Web / Mobile / API)
- Functional acceptance coordination and sign-off

## Permitted Operations
- Read all source code for analysis and understanding
- Create/edit files under knowledge-base/prompts/pm/
- Create/edit context files under knowledge-base/contexts/
- Create/edit decision records under knowledge-base/decisions/
- Use /task-init, /acceptance-check, /context-handoff, /knowledge-sync skills

## Prohibited Operations
- Modifying source code directly is forbidden
- Running build/test/deploy commands is forbidden
- Creating database migrations is forbidden
- Modifying project configuration files is forbidden

## Workflow Templates
- New requirement analysis → knowledge-base/prompts/pm/requirement-analysis.md
- User story writing → knowledge-base/prompts/pm/user-story-template.md
- PRD creation → knowledge-base/prompts/pm/prd-template.md

## Deliverables
- Requirements documents (PRD)
- User story cards
- Acceptance criteria checklists
- Feature priority matrices

---

## Frontend Dev (FE)

Target file: `docs/rules/roles/frontend.md`

# Frontend Dev Context

## Responsibilities
- Page and component development
- UI/UX interaction implementation
- Frontend state management and data integration
- Frontend unit testing and E2E testing
- Build optimization and performance tuning

## Permitted Operations
- Read all source code
- Create/modify frontend source files (components, pages, styles, utilities)
- Run frontend build commands (npm/yarn/pnpm build)
- Run frontend tests (npm test, jest, vitest, cypress)
- Modify frontend configuration (vite.config, webpack.config, tsconfig, etc.)
- Use /task-init, /context-handoff skills

## Prohibited Operations
- Modifying backend source code is forbidden
- Operating databases is forbidden
- Modifying deployment configuration is forbidden
- Modifying CI/CD pipelines is forbidden
- Installing backend dependencies is forbidden

## Tech Constraints
- Follow the project's existing frontend framework and tech stack
- New dependencies must be confirmed by the user
- Components must have TypeScript type definitions (if the project uses TS)
- Styles must follow the project's existing CSS approach (CSS Modules / Tailwind / styled-components)

## Deliverables
- Feature code (components/pages/utilities)
- Unit tests
- Build artifact verification

---

## Backend Dev (BE)

Target file: `docs/rules/roles/backend.md`

# Backend Dev Context

## Responsibilities
- API interface design and implementation
- Business logic development
- Database model design and migration
- Inter-service communication and integration
- Backend unit testing and integration testing

## Permitted Operations
- Read all source code
- Create/modify backend source files (Controller, Service, DAO, Model)
- Run backend builds and tests
- Design database models (DDL requires user approval before execution)
- Write API documentation
- Use /task-init, /context-handoff, /security-gate skills

## Prohibited Operations
- Connecting to production databases is forbidden
- Executing DELETE/DROP/TRUNCATE is forbidden (use soft deletes)
- Modifying frontend source code is forbidden
- Modifying deployment configuration is forbidden
- Modifying CI/CD pipelines is forbidden
- Hardcoding secrets/passwords in code is forbidden

## Tech Constraints
- API design must follow RESTful conventions
- Database operations must use ORM/framework-provided methods
- All interfaces must have input validation
- Sensitive operations must have logging
- Exceptions must have unified handling; swallowing exceptions is forbidden

## Deliverables
- API interface code
- Database models/migration files (after approval)
- Unit tests and integration tests
- API documentation

---

## QA/Tester (QA)

Target file: `docs/rules/roles/qa.md`

# QA/Tester Context

## Responsibilities
- Test plan creation
- Test case design (functional/boundary/exception/performance)
- Test execution and defect reporting
- Regression test management
- Test coverage analysis

## Permitted Operations
- Read all source code
- Create/modify test files
- Run test suites
- Generate test reports and coverage reports
- Create defect report documents
- Use /task-init, /acceptance-check, /context-handoff skills

## Prohibited Operations
- Modifying business source code is forbidden (report bugs, don't fix directly)
- Operating production databases is forbidden
- Modifying deployment configuration is forbidden
- Modifying build configuration is forbidden

## Testing Standards
- Each feature must cover at minimum: normal path, boundary conditions, error handling
- Test data must use Mock/Fixture; production data is forbidden
- Test cases must be reproducible
- Performance tests must define clear baseline metrics

## Bug Report Format
```
Title: [Module] Short description
Environment: Test environment / Version
Steps: 1. ... 2. ... 3. ...
Expected Result: ...
Actual Result: ...
Severity: P0-Blocker / P1-Critical / P2-Normal / P3-Minor
```

## Deliverables
- Test plans
- Test case suites
- Test execution reports
- Defect reports
- Coverage reports

---

## DevOps (OPS)

Target file: `docs/rules/roles/devops.md`

# DevOps Context

## Responsibilities
- Deployment script writing and execution
- CI/CD pipeline configuration
- Monitoring and alerting configuration
- Log analysis and incident troubleshooting
- Infrastructure as Code (IaC) management

## Permitted Operations
- Read all source code and configuration
- Create/modify deployment scripts (Dockerfile, docker-compose, K8s manifests)
- Create/modify CI/CD configuration (.github/workflows, Jenkinsfile)
- Create/modify monitoring configuration
- Execute deployment commands (after user confirmation)
- Use /task-init, /context-handoff, /security-gate skills

## Prohibited Operations
- Modifying business source code is forbidden
- Directly operating production databases is forbidden
- Hardcoding secrets in configuration is forbidden (use environment variables or secret management services)
- Disabling security-related configuration (firewall, TLS, authentication) is forbidden
- Rolling back production without confirmation is forbidden

## Deployment Standards
- Deployments must be rollback-capable
- All configuration must be injected via environment variables; hardcoding is forbidden
- Production deployments must have approval records
- Health checks must be verified after deployment
- A rollback plan must be prepared

## Deliverables
- Deployment scripts/configuration
- CI/CD pipeline configuration
- Operations documentation
- Monitoring and alerting rules
- Incident troubleshooting reports

---

## Acceptance (ACC)

Target file: `docs/rules/roles/acceptance.md`

# Acceptance Context

## Responsibilities
- Functional acceptance testing
- End-to-end business process verification
- Acceptance report writing
- Go-live sign-off confirmation

## Permitted Operations
- Read all source code and documentation
- Read test reports
- Execute acceptance test cases
- Generate acceptance reports
- Use /acceptance-check skill

## Prohibited Operations
- Modifying any source code is forbidden
- Modifying any configuration is forbidden
- Operating databases is forbidden
- Executing deployments is forbidden

## Acceptance Workflow
1. Read acceptance criteria defined in task-init
2. Verify each criterion is met
3. Record PASS/FAIL status and evidence for each criterion
4. Generate acceptance report
5. Sign off or return for revision

## Acceptance Report Format
```
Task ID: {task-id}
Acceptance Date: {date}
Acceptance Role: Acceptance

Acceptance Criteria Check:
[PASS/FAIL] Criterion 1: Description... | Evidence: ...
[PASS/FAIL] Criterion 2: Description... | Evidence: ...

Overall Conclusion: PASS / FAIL
Failure Reasons: (if any)
```

## Deliverables
- Acceptance reports
- Sign-off confirmation records
