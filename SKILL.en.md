---
name: create-workflow-skill
description: "One-click generator for a multi-role team collaboration workflow environment based on Claude Code native capabilities. Automatically creates complete directory structure, role system, guardrail rules, skill templates, and knowledge base. Triggers: 'create workflow', 'init workflow', 'setup dev workflow'."
---

# /create-workflow-skill — AI Development Workflow Environment Generator

You are a workflow environment builder. Guide the user to quickly create a complete multi-role team collaboration workflow environment based on Claude Code native capabilities.

**Core Principle**: Only generate a generic workflow framework. No project-specific business data is included. Users fill in project-specific details after generation.

## Template Loading

When executing Steps 3-10, first locate the template directory, then Read the corresponding template files as needed:

    Glob('**/create-workflow-skill/templates/en/*.md')

---

## Step 1: Gather Basic Project Information

Ask the user for the following information (use defaults if not provided):

1. **Project Name** — Required
2. **Project Root** — Required, workflow files will be generated in this directory
3. **Language Preference** — English (default) | Chinese | Bilingual
4. **Required Roles** (multi-select, all selected by default):
   - Product Manager (PM) — Requirements analysis, PRD, acceptance coordination
   - Frontend Dev (FE) — Page components, UI implementation
   - Backend Dev (BE) — API, business logic, database
   - QA/Tester (QA) — Test plans, test cases, defect reports
   - DevOps (OPS) — Deployment, CI/CD, monitoring
   - Acceptance (ACC) — Functional acceptance, sign-off
5. **Required Skills** (multi-select, all selected by default):
   - /task-init — Task initialization & prompt template
   - /context-handoff — Context compression & handoff
   - /knowledge-sync — Knowledge base management
   - /acceptance-check — Acceptance check
   - /security-gate — Security red-line guard
   - /generate-help — Help documentation generation
6. **Custom Roles** (optional) — Format: ID-Name-Duties
7. **Custom Skills** (optional) — Format: Name-Trigger-Purpose

After user confirmation, proceed to Step 2.

---

## Step 2: Generate Directory Structure

Generate the following structure under the user-specified project root (only generate files for user-selected roles and skills):

    {project-root}/
    ├── CLAUDE.md
    ├── ROLES.md
    ├── .claude/
    │   ├── rules/
    │   │   └── guardrails/{boundaries,git-workflow,redlines}.md
    │   ├── skills/{each selected skill}/SKILL.md
    │   └── settings.json
    ├── docs/
    │   └── rules/
    │       ├── roles/{each selected role}.md
    │       └── workflows/{feature-dev,hotfix,release}.md
    ├── knowledge-base/
    │   ├── INDEX.md
    │   ├── contexts/{active,archived}/
    │   ├── prompts/{each selected role}/
    │   └── decisions/
    └── docs/

---

## Steps 3-10: Generate Files from Templates

Execute the following steps sequentially. For each step, Read the corresponding template file, dynamically replace placeholders based on information collected in Step 1 and user selections, then generate the target files.

| Step | Template File | Target | Description |
|------|--------------|--------|-------------|
| Step 3 | templates/en/claude-md.md | CLAUDE.md | Main entry configuration |
| Step 4 | templates/en/roles-md.md | ROLES.md | Role registry & permission matrix |
| Step 5 | templates/en/role-contexts.md | docs/rules/roles/*.md | Role context files |
| Step 6 | templates/en/guardrails.md | .claude/rules/guardrails/*.md | Guardrail rule files |
| Step 7 | templates/en/workflows.md | docs/rules/workflows/*.md | Workflow files |
| Step 8a | templates/en/skills-core.md | .claude/skills/{task-init,context-handoff,knowledge-sync}/SKILL.md | Core skills |
| Step 8b | templates/en/skills-quality.md | .claude/skills/{acceptance-check,security-gate,generate-help}/SKILL.md | Quality skills |
| Step 9 | templates/en/knowledge-base.md | knowledge-base/ | Knowledge base structure |
| Step 10 | templates/en/settings.md | .claude/settings.json | Permission configuration |

**Execution Requirements**:
- Only generate files for roles and skills selected by the user in Step 1
- Do not generate files for unselected roles/skills
- Dynamically replace {project-name}, {project-root}, and other placeholders

---

## Step 11: Output Generation Report

After all files have been generated, display the report to the user:

    ╔══════════════════════════════════════════════════════════╗
    ║           Workflow Environment Ready!                     ║
    ╠══════════════════════════════════════════════════════════╣
    ║ Project: {project-name}                                   ║
    ║ Path:    {project-root}                                   ║
    ╠══════════════════════════════════════════════════════════╣
    ║ Generated Files:                                          ║
    ║   CLAUDE.md          — Main entry configuration           ║
    ║   ROLES.md           — Role registry                      ║
    ║   {N} role files      — docs/rules/roles/                 ║
    ║   3 guardrail rules   — .claude/rules/guardrails/         ║
    ║   3 workflows         — docs/rules/workflows/             ║
    ║   {N} skill files     — .claude/skills/                   ║
    ║   Knowledge base      — knowledge-base/                   ║
    ║   settings.json       — .claude/settings.json             ║
    ╠══════════════════════════════════════════════════════════╣
    ║ Quick Start:                                              ║
    ║   1. Fill in your project information in CLAUDE.md        ║
    ║   2. Run /task-init to start your first task              ║
    ║   3. Use /generate-help to generate help documentation    ║
    ╠══════════════════════════════════════════════════════════╣
    ║ Customization:                                            ║
    ║   - Add roles: Create a new file in docs/rules/roles/     ║
    ║   - Add skills: Create a new directory and SKILL.md in    ║
    ║                  .claude/skills/                           ║
    ║   - Add templates: Use /knowledge-sync to manage the      ║
    ║                     knowledge base                        ║
    ╚══════════════════════════════════════════════════════════╝

---

## Execution Constraints

1. **No project-specific data**: Only provide placeholders and template structures
2. **User confirmation first**: Each critical step requires user confirmation before proceeding
3. **Idempotency**: If the target directory already contains workflow files, show differences first and let the user decide to overwrite or merge
4. **Minimal generation**: Only generate files for user-selected roles and skills
5. **Structure and content separation**: Generate framework structure; users fill in content via skills
