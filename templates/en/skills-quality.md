# Step 8b: Quality Skill File Templates

Generate 4 skill files. Each skill requires a SKILL.md in its corresponding directory.

---

## Skill 4: /acceptance-check

Target file: `.claude/skills/acceptance-check/SKILL.md`

frontmatter:

    ---
    name: acceptance-check
    description: "Check task completion against acceptance criteria item by item and generate an acceptance report. Triggers: user says 'acceptance', 'check completion', 'acceptance check', 'verify', or when a task marked as complete needs verification. Applicable roles: Product Manager (PM), QA/Tester (QA), Acceptance (ACC)."
    ---

Content:

# /acceptance-check — Acceptance Check

You are the acceptance checker. Your responsibility is to verify task completion against the acceptance criteria defined in task-init, item by item, and generate an objective acceptance report.

## Execution Steps

### Step 1: Load Acceptance Criteria

1. Ask the user for the task ID to accept, or obtain it from the current session context
2. Read acceptance criteria from `knowledge-base/contexts/active/{task-id}.md`
3. If no context file is found, ask the user to provide acceptance criteria

### Step 2: Item-by-Item Verification

For each acceptance criterion, perform a check:

- **Code criteria**: Check if relevant files exist and contain the expected implementation
- **Test criteria**: Verify test files exist and tests cover key paths
- **Documentation criteria**: Check if documentation files exist and content is complete
- **Configuration criteria**: Check if configuration files are correct

Each criterion's check result must include:
- Status: PASS / FAIL / SKIP (skip requires explanation)
- Evidence: Specific file paths, code snippets, or command output

### Step 3: Generate Acceptance Report

```
╔══════════════════════════════════════════════════════════╗
║                    Acceptance Report                      ║
╠══════════════════════════════════════════════════════════╣
║ Task ID    : {task-id}                                    ║
║ Date       : {date}                                       ║
║ Role       : {role}                                       ║
║ Conclusion : {PASS / FAIL}                                ║
╠══════════════════════════════════════════════════════════╣
║ Check Details:                                            ║
║                                                          ║
║ [PASS] Criterion 1: {description}                        ║
║        Evidence: {file path or check result}              ║
║                                                          ║
║ [FAIL] Criterion 2: {description}                        ║
║        Reason: {why it failed}                            ║
║        Suggestion: {how to fix}                           ║
║                                                          ║
║ [PASS] Criterion 3: {description}                        ║
║        Evidence: {file path or check result}              ║
╠══════════════════════════════════════════════════════════╣
║ Statistics: {pass}/{total} passed ({percentage}%)         ║
║                                                          ║
║ Failed Items Summary:                                     ║
║ - {Criterion 2}: {brief reason}                           ║
╚══════════════════════════════════════════════════════════╝
```

### Step 4: Handle Results

- **All passed**: Update context status to completed, archive to archived/
- **Any failures**: Keep status as in-progress, add failed items to Open Items
- Update `knowledge-base/INDEX.md`

## Acceptance Principles

1. **Objective verification**: Only consider factual evidence, no subjective judgments
2. **Item-by-item recording**: Every criterion must have a clear PASS/FAIL and evidence
3. **Traceable**: Evidence in the report must be verifiable (file paths, command output)
4. **No overstepping**: Acceptance role only judges pass/fail, does not execute modifications

---

## Skill 5: /security-gate

Target file: `.claude/skills/security-gate/SKILL.md`

frontmatter:

    ---
    name: security-gate
    description: "Perform a security red-line review before executing sensitive operations. Triggers: user says 'security check', or when database write operations, deployment operations, force-push, secret operations, or other sensitive behaviors are detected. Applicable roles: Backend Dev (BE), DevOps (OPS)."
    ---

Content:

# /security-gate — Security Red-Line Guard

You are the security guard. Before any sensitive operation is executed, you must perform a security review to ensure the operation does not violate red-line rules.

## Trigger Scenarios

The following operations must pass security-gate review before execution:

1. **Database operations**: CREATE/ALTER/DROP TABLE, INSERT/UPDATE/DELETE, migration file generation
2. **Dangerous Git operations**: push --force, reset --hard, rebase (already-pushed commits)
3. **Deployment operations**: Any production environment deployment
4. **Dependency changes**: Modifications to package.json/go.mod/requirements.txt, etc.
5. **Configuration changes**: CI/CD, environment variables, secret management configuration
6. **File deletions**: Batch delete operations

## Review Process

### Step 1: Identify Operation Type

Analyze the user's request or pending command, classify as:
- DATABASE
- GIT
- DEPLOY
- DEPENDENCY
- CONFIG
- DESTRUCTIVE

### Step 2: Red-Line Cross-Check

Read the red-line rules and compare item by item:

```
Security Review Checklist
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
Operation Description: {operation to be executed}
Operation Type: {DATABASE|GIT|DEPLOY|...}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

[PASS/FAIL] DB-01: Not a production database connection
[PASS/FAIL] DB-02: No DELETE/DROP/TRUNCATE
[PASS/FAIL] DATA-01: No secret leakage
[PASS/FAIL] GIT-01: Not force-push to protected branch
[PASS/FAIL] EXEC-01: Not a destructive command
... (only list rules relevant to the current operation)

Conclusion: APPROVED / BLOCKED
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Step 3: Handle Results

**APPROVED (Passed)**:
- Output review approval confirmation
- Allow operation to continue

**BLOCKED (Blocked)**:
- Clearly state which red-line rule was violated (cite the ID)
- Refuse to execute the operation
- Provide a compliant alternative approach

## Review Principles

1. **Err on the side of caution**: Block by default when in doubt; require explicit user confirmation
2. **Cite rules**: Every block must cite a specific red-line ID
3. **Provide alternatives**: A compliant alternative must be offered after blocking
4. **Cannot be bypassed**: Red-line rules cannot be circumvented even if the user insists

---

## Skill 6: /generate-help

Target file: `.claude/skills/generate-help/SKILL.md`

frontmatter:

    ---
    name: generate-help
    description: "Generate a docs/help.html usage help document from the current workflow configuration. Triggers: user says 'generate help docs', 'update help page', 'generate help'. Applicable roles: Product Manager (PM), DevOps (OPS)."
    ---

Content:

# /generate-help — Generate Help Documentation

You are the documentation generator. Your responsibility is to read all configuration from the current workflow environment and generate a complete `docs/help.html` help document.

## Execution Steps

### Step 1: Collect Configuration Information

Read the following files for the latest configuration:
1. `CLAUDE.md` — Project overview, red-line rules
2. `ROLES.md` — Role matrix, permission matrix
3. `docs/rules/roles/*.md` — Detailed role contexts
4. `.claude/rules/guardrails/*.md` — Guardrail rules
5. `docs/rules/workflows/*.md` — Workflow definitions
6. `.claude/skills/*/SKILL.md` — Skill descriptions
7. `knowledge-base/INDEX.md` — Knowledge base index

### Step 2: Generate HTML

Generate a pure HTML single file with requirements:
- Professional, clean design style
- Embedded CSS (no external resource dependencies)
- Print support (@media print hides sidebar)
- Client-side table of contents navigation (sidebar search filter + scroll highlight)
- All examples copyable (click to copy commands)
- Responsive layout (mobile hamburger menu + sidebar collapse)

#### HTML Design Specifications

**Layout Structure**: Fixed sidebar (280px) + main content area (max-width: 960px) two-column layout

**CSS Variable Theme**:
- `--primary` blue: primary color, links, active state
- `--danger` red: red-line rules, version lock highlights
- `--success` green: green zone, pass status
- `--warning` yellow: warnings, notes

**Required UI Components**:
- `.badge-{role}`: Role badges (each role has its color: pm-blue/fe-green/be-orange/qa-red/ops-purple/acc-gray)
- `.card` + `.card-danger/success/warning/info`: Info cards (left color bar to distinguish types)
- `.step` + `.step-num`: Step components (circular number + content area)
- `.flow` + `.flow-item` + `.flow-arrow`: Flow chart components (horizontal arrow flow)
- `.version-lock`: Version lock highlight labels
- `.cmd`: Command line style (dark background, monospace font, click to copy)
- `.grid-2` / `.grid-3`: Grid layouts (responsive, mobile single column)
- `.search-box`: Sidebar search box

**Interactive Features** (embedded JavaScript):
- `updateNav()`: Scroll position tracking, auto-highlight current section navigation
- `filterNav(query)`: Search filter sidebar navigation items
- `.cmd` click to copy to clipboard
- Mobile menu toggle

### Step 3: Content Structure

help.html must contain the following 13 sections:
1. System Overview — What it is, why, what problems it solves (with tech architecture tree + loading strategy explanation)
2. Quick Start — 3-step getting started guide
3. Role Descriptions — Detailed descriptions of 6 roles (with permission matrix table)
4. Skill Reference — Usage instructions and triggers for 6 skills
5. Task Workflow — Complete task lifecycle (with task type boundary table)
6. Prompt Templates — Template format and usage (with filled examples)
7. Knowledge Base Management — How to use the knowledge base
8. Security Rules — Red-line rules table (DB/GIT/DATA/EXEC IDs) + Green zone + Coding security
9. Development Constraints — Unit test constraints, frontend dev constraints, database dev constraints, general boundary rules
10. Context Sharing — Five-layer context architecture table (L1-L5) + Token savings data + Cross-session handoff flow
11. Cross-Role Collaboration — Feature development/Hotfix/Release workflows (with flow chart components)
12. Custom Configuration — How to extend roles/skills/templates (with notes on modifying red-line rules)
13. FAQ — Common questions (including architecture migration reasons, --dangerously-skip-permissions explanation)

### Step 4: Save and Verify

1. Save to `docs/help.html`
2. Verify HTML format is correct
3. Report generation results

---

## Skill 7: /export-prompt-log

Target file: `.claude/skills/export-prompt-log/SKILL.md`

frontmatter:

    ---
    name: export-prompt-log
    description: "Export prompt logs by month. Triggers: user says 'export prompt log', 'export log', or when monthly prompt reasoning records need to be summarized. Applicable roles: All roles."
    ---

Content:

# /export-prompt-log — Monthly Prompt Log Export

You are the prompt log export tool. Your responsibility is to filter all prompt logs for a user-specified month from the `knowledge-base/prompt-logs/` directory and export them as a structured Markdown file.

## Execution Steps

### Step 1: Get Export Month

If the user provided a month parameter when triggering (e.g. `/export-prompt-log 2026-06`), use it directly.

If not provided, ask the user:

```
Please enter the month to export (format: YYYY-MM), e.g.: 2026-06
```

### Step 2: Scan Prompt Logs

1. Read all `.md` files under `knowledge-base/prompt-logs/` directory
2. Filter records for the specified month by the date field (second segment, YYYYMMDD) in the filename
   - Filename format: `{name}-{date}-{iteration}-{role}-{seq}.md`
   - Extract the `{date}` field (YYYYMMDD), match the year-month portion (YYYYMM)
   - Example: User specifies `2026-06`, match all files where date starts with `202606`
3. If no records exist for that month, inform the user and end:
   ```
   No prompt logs found for the specified month ({YYYY-MM}).
   ```

### Step 3: Read and Summarize

For each filtered file:
1. Read file content
2. Extract metadata from frontmatter (developer, date, iteration, role, record-count)
3. Extract all reasoning records (user original + semantic understanding + completion report)

Sort by the following dimensions:
- Primary sort: date (ascending)
- Secondary sort: developer (alphabetical)
- Tertiary sort: sequence number (ascending)

### Step 4: Generate Export File

Export filename: `prompt-log-{YYYY-MM}.md`
Save path: `knowledge-base/exports/prompt-log-{YYYY-MM}.md`

Export format:

```markdown
---
export-month: {YYYY-MM}
export-date: {full datetime of export}
total-files: {total file count}
total-records: {total reasoning record count}
developers: [{deduplicated developer list}]
---

# Prompt Log Monthly Report

## Export Info

| Field | Value |
|-------|-------|
| Export Month | {YYYY-MM} |
| Export Date | {full datetime of export} |
| Log File Count | {total file count} |
| Total Records | {total reasoning record count} |
| Developers | {deduplicated developer list} |

---

## Summary by Date

### {YYYY-MM-DD} — {developer} — {iteration} — {role}

> Source file: `{original filename}`

#### Record 1

**User Natural Language Original:**

> {user original input}

**Semantic Understanding Confirmation:**

{semantic understanding confirmation content}

**Completion Report:**

{execution result summary}

---

#### Record 2

**User Natural Language Original:**

> {user original input}

**Semantic Understanding Confirmation:**

{semantic understanding confirmation content}

**Completion Report:**

{execution result summary}

---

(More records from the same file...)

---

### {next date} — {developer} — {iteration} — {role}

> Source file: `{original filename}`

(Records from this file...)

---

## Statistics Summary

| Developer | Log Files | Records | Iterations |
|-----------|----------|---------|------------|
| {name1} | {n} | {m} | {iterations} |
| {name2} | {n} | {m} | {iterations} |
| **Total** | **{total_files}** | **{total_records}** | |
```

### Step 5: Output Confirmation

Display to the user:
1. Export file save path
2. Statistics summary (file count, record count, developer list)
3. Inform the user that the export file can be found in the `knowledge-base/exports/` directory

Output format:
```
✅ Prompt log export complete

📁 File path: knowledge-base/exports/prompt-log-{YYYY-MM}.md
📊 Statistics: {N} log files, {M} reasoning records
👥 Developers: {name1}, {name2}, ...
📅 Date range: {earliest date} ~ {latest date}
```

## Notes

1. If an export file already exists for the target month, inform the user first and confirm whether to overwrite
2. The export file only summarizes existing records; it does not modify original prompt-logs files
3. Ensure the `knowledge-base/exports/` directory exists; create it if it doesn't
4. If a malformed log file is encountered during export, skip it and note it at the end of the export file
