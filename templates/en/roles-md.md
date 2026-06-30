# Step 4: ROLES.md Template

Target file: `{project-root}/ROLES.md`

Dynamically generated based on user-selected roles. Only include selected roles and skills in the matrices.

---

## Generated Content

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

---

## Dynamic Generation Notes

- Only include user-selected role columns in matrices
- Only include user-selected skill rows in skill mapping
- Custom roles are added to matrices using user-provided information
