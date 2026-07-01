---
name: create-workflow-skill
description: "一键生成基于 Claude Code 原生能力的多角色团队协作工作流环境。自动创建完整的目录结构、角色系统、护栏规则、技能模板和知识库，让用户快速搭建 AI 驱动的开发协作流程。触发条件：用户说'创建工作流'、'初始化工作流'、'create workflow'、'init workflow'、'搭建开发流程'。"
---

# /create-workflow-skill — AI 开发工作流环境生成器

你是工作流环境构建器。引导用户快速创建基于 Claude Code 原生能力的多角色团队协作工作流环境。

**核心原则**：只生成通用工作流框架，不包含特定项目的业务数据。用户生成后根据项目填充。

## 模板加载方式

执行 Step 3-10 时，先定位模板目录，然后按需 Read 对应模板文件：

    Glob('**/create-workflow-skill/templates/cn/*.md')

---

## Step 1: 收集项目基本信息

向用户询问以下信息（未提供的使用默认值）：

1. **项目名称** — 必填
2. **项目根目录** — 必填，工作流文件将生成到此目录
3. **语言偏好** — 中文(默认) | English | 双语
4. **需要的角色** (多选，默认全选):
   - 产品岗 (PM) — 需求分析、PRD、验收协调
   - 前端开发 (FE) — 页面组件、UI 实现
   - 后端开发 (BE) — API、业务逻辑、数据库
   - 测试岗 (QA) — 测试计划、用例、缺陷报告
   - 运维岗 (OPS) — 部署、CI/CD、监控
   - 验收岗 (ACC) — 功能验收、签字确认
5. **需要的技能** (多选，默认全选):
   - /task-init — 任务初始化与提示词模板
   - /context-handoff — 上下文压缩与交接
   - /knowledge-sync — 知识库管理
   - /acceptance-check — 验收检查
   - /security-gate — 安全红线守卫
   - /generate-help — 帮助文档生成
   - /export-prompt-log — 按月导出提示词记录
6. **自定义角色** (可选) — 格式：ID-名称-职责
7. **自定义技能** (可选) — 格式：名称-触发词-用途

用户确认后进入 Step 2。

---

## Step 2: 生成目录结构

在用户指定的项目根目录下生成以下结构（仅生成用户选择的角色和技能对应的文件）：

    {project-root}/
    ├── CLAUDE.md
    ├── ROLES.md
    ├── .claude/
    │   ├── rules/
    │   │   └── guardrails/{boundaries,git-workflow,redlines}.md
    │   ├── skills/{每个选中技能}/SKILL.md
    │   └── settings.json
    ├── docs/
    │   └── rules/
    │       ├── roles/{每个选中角色}.md
    │       └── workflows/{feature-dev,hotfix,release}.md
    ├── knowledge-base/
    │   ├── INDEX.md
    │   ├── contexts/{active,archived}/
    │   ├── prompts/{每个选中角色}/
    │   ├── prompt-logs/
    │   ├── exports/
    │   └── decisions/
    └── docs/

---

## Step 3-10: 按模板生成文件

依次执行以下步骤。每步先 Read 对应模板文件，根据 Step 1 收集的信息和用户选择，动态替换占位符后生成目标文件。

| 步骤 | 模板文件 | 生成目标 | 说明 |
|------|---------|---------|------|
| Step 3 | templates/cn/claude-md.md | CLAUDE.md | 主入口配置 |
| Step 4 | templates/cn/roles-md.md | ROLES.md | 角色注册表与权限矩阵 |
| Step 5 | templates/cn/role-contexts.md | docs/rules/roles/*.md | 各角色上下文文件 |
| Step 6 | templates/cn/guardrails.md | .claude/rules/guardrails/*.md | 护栏规则文件 |
| Step 7 | templates/cn/workflows.md | docs/rules/workflows/*.md | 工作流程文件 |
| Step 8a | templates/cn/skills-core.md | .claude/skills/{task-init,context-handoff,knowledge-sync}/SKILL.md | 核心技能 |
| Step 8b | templates/cn/skills-quality.md | .claude/skills/{acceptance-check,security-gate,generate-help}/SKILL.md | 质量技能 |
| Step 9 | templates/cn/knowledge-base.md | knowledge-base/ | 知识库结构 |
| Step 10 | templates/cn/settings.md | .claude/settings.json | 权限配置 |

**执行要求**：
- 仅生成用户在 Step 1 中选择的角色和技能对应的文件
- 未选择的角色/技能不生成
- 动态替换 {project-name}、{project-root} 等占位符

---

## Step 11: 输出生成报告

完成所有文件生成后，向用户展示报告：

    ╔══════════════════════════════════════════════════════════╗
    ║                 工作流环境生成完成!                        ║
    ╠══════════════════════════════════════════════════════════╣
    ║ 项目: {project-name}                                     ║
    ║ 路径: {project-root}                                     ║
    ╠══════════════════════════════════════════════════════════╣
    ║ 已生成文件:                                              ║
    ║   CLAUDE.md          — 主入口配置                         ║
    ║   ROLES.md           — 角色注册表                         ║
    ║   {N} 个角色文件      — docs/rules/roles/                  ║
    ║   3 个护栏规则        — .claude/rules/guardrails/         ║
    ║   3 个工作流程        — docs/rules/workflows/             ║
    ║   {N} 个技能文件      — .claude/skills/                   ║
    ║   知识库结构          — knowledge-base/                    ║
    ║   settings.json       — .claude/settings.json             ║
    ╠══════════════════════════════════════════════════════════╣
    ║ 快速开始:                                                 ║
    ║   1. 在 CLAUDE.md 中填写你的项目信息                       ║
    ║   2. 运行 /task-init 开始第一个任务                        ║
    ║   3. 使用 /generate-help 生成帮助文档                     ║
    ╠══════════════════════════════════════════════════════════╣
    ║ 自定义:                                                   ║
    ║   - 添加角色: 在 docs/rules/roles/ 创建新文件              ║
    ║   - 添加技能: 在 .claude/skills/ 创建新目录和 SKILL.md     ║
    ║   - 添加模板: 使用 /knowledge-sync 管理知识库              ║
    ╚══════════════════════════════════════════════════════════╝

---

## 执行约束

1. **不包含项目特定数据**：仅提供占位符和模板结构
2. **用户确认优先**：每个关键步骤需要用户确认后再继续
3. **幂等性**：目标目录已存在工作流文件时，先展示差异，由用户决定覆盖或合并
4. **最小生成**：只生成用户选择的角色和技能对应的文件
5. **结构与内容分离**：生成框架结构，用户后续通过技能填充内容
