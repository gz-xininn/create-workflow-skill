# create-workflow-skill

> 一键生成基于 Claude Code 的多角色团队协作 AI 开发工作流环境。

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-Skill-blue)](https://docs.anthropic.com/en/docs/claude-code)

---

## 这是什么？

`create-workflow-skill` 是一个 Claude Code 技能，用于生成完整的多角色团队协作工作流环境。它会自动创建完整的目录结构、角色系统、护栏规则、技能模板和知识库，让你快速搭建 AI 驱动的开发协作流程。

### 核心特性

- **多角色系统** — 产品岗、前端开发、后端开发、测试岗、运维岗、验收岗，配备 RBAC 权限矩阵
- **任务工作流** — 语义理解 → 任务初始化 → 执行阶段 → 上下文交接
- **安全护栏** — 数据库、Git、数据、执行红线规则，不可绕过
- **知识库** — 上下文压缩与交接，跨会话节省 70-80% token
- **内置技能** — 7 个开箱即用的技能：task-init、context-handoff、knowledge-sync、acceptance-check、security-gate、generate-help、export-prompt-log
- **完全可定制** — 支持添加自定义角色、技能和提示词模板

---

## 快速开始

### 1. 安装技能

将 `SKILL.md` 和 `templates/` 目录复制到你的 Claude Code 技能目录：

```bash
# 创建技能目录
mkdir -p .claude/skills/create-workflow-skill

# 复制技能文件和模板
cp SKILL.md .claude/skills/create-workflow-skill/SKILL.md
cp -r templates .claude/skills/create-workflow-skill/templates
```

或者直接克隆此仓库：

```bash
git clone https://github.com/gz-xininn/create-workflow-skill.git
cp create-workflow-skill/SKILL.md /your-project/.claude/skills/create-workflow-skill/SKILL.md
cp -r create-workflow-skill/templates /your-project/.claude/skills/create-workflow-skill/templates
```

> **提示**：英文版请将 `SKILL.en.md` 复制为 `SKILL.md`：
> ```bash
> cp SKILL.en.md .claude/skills/create-workflow-skill/SKILL.md
> ```

### 2. 运行技能

在 Claude Code 中输入：

```
/create-workflow-skill
```

或者使用自然语言：

```
创建工作流
init workflow
搭建开发流程
```

### 3. 按向导操作

技能将引导你完成以下步骤：

1. **项目信息** — 输入项目名称和根目录
2. **选择角色** — 选择需要的角色（默认：全部 6 个）
3. **选择技能** — 选择需要包含的技能（默认：全部 7 个）
4. **生成文件** — 所有文件自动创建
5. **自定义配置** — 在 CLAUDE.md 中填写项目特定信息

---

## 生成的结构

```
your-project/
├── CLAUDE.md                           # 主入口配置（Claude Code 自动加载）
├── ROLES.md                            # 角色注册表与权限矩阵
├── .claude/
│   ├── rules/
│   │   └── guardrails/
│   │       ├── boundaries.md           # 执行边界
│   │       ├── git-workflow.md         # Git 工作流规范
│   │       └── redlines.md             # 红线规则
│   ├── skills/
│   │   ├── task-init/SKILL.md          # 任务初始化
│   │   ├── context-handoff/SKILL.md    # 上下文压缩与交接
│   │   ├── knowledge-sync/SKILL.md     # 知识库管理
│   │   ├── acceptance-check/SKILL.md   # 验收检查
│   │   ├── security-gate/SKILL.md      # 安全护栏
│   │   ├── generate-help/SKILL.md      # 帮助文档生成器
│   │   └── export-prompt-log/SKILL.md  # 按月导出提示词记录
│   └── settings.json                   # Claude Code 配置
├── docs/
│   ├── rules/
│   │   ├── roles/
│   │   │   ├── pm.md                   # 产品经理上下文
│   │   │   ├── frontend.md             # 前端开发上下文
│   │   │   ├── backend.md              # 后端开发上下文
│   │   │   ├── qa.md                   # 测试岗上下文
│   │   │   ├── devops.md               # 运维岗上下文
│   │   │   └── acceptance.md           # 验收岗上下文
│   │   └── workflows/
│   │       ├── feature-dev.md          # 功能开发流程
│   │       ├── hotfix.md               # 热修复流程
│   │       └── release.md              # 发版流程
│   └── help.html                       # 帮助文档（由 /generate-help 生成）
├── knowledge-base/
│   ├── INDEX.md                        # 知识库索引
│   ├── contexts/
│   │   ├── active/                     # 进行中的任务上下文
│   │   └── archived/                   # 已归档的上下文
│   ├── prompts/                        # 提示词模板（按角色分类）
│   ├── prompt-logs/                    # 提示词推理记录（由 /context-handoff 自动生成）
│   ├── exports/                        # 导出文件（由 /export-prompt-log 生成）
│   └── decisions/                      # 架构决策记录
```

---

## 角色系统

| 角色 ID | 角色名称 | 核心职责 |
|---------|---------|---------|
| pm | 产品岗 | 需求管理、PRD 撰写、验收协调 |
| fe | 前端开发 | 页面、组件、UI 实现 |
| be | 后端开发 | API、业务逻辑、数据库 |
| qa | 测试岗 | 测试计划、测试用例、缺陷报告 |
| ops | 运维岗 | 部署、CI/CD、监控 |
| acc | 验收岗 | 功能验收、签收确认 |

### 权限矩阵

| 操作 | PM | FE | BE | QA | OPS | ACC |
|------|----|----|----|----|-----|-----|
| 读取源码 | Y | Y | Y | Y | Y | Y |
| 修改源码 | - | Y | Y | - | - | - |
| 运行构建 | - | Y | Y | - | Y | - |
| 运行测试 | - | Y | Y | Y | - | - |
| 数据库操作 | - | - | Y | - | - | - |
| 部署 | - | - | - | - | Y | - |
| 管理知识库 | Y | Y | Y | Y | Y | Y |
| 验收签收 | Y | - | - | Y | - | Y |

---

## 内置技能

| 技能 | 触发词 | 说明 |
|------|--------|------|
| `/task-init` | new task、start、begin | 语义理解 + 结构化任务确认单生成 |
| `/context-handoff` | save context、wrap up、done | 压缩并保存上下文 + 自动生成提示词记录，用于跨会话交接 |
| `/knowledge-sync` | sync、add template | 知识库增删改查与索引管理 |
| `/acceptance-check` | verify、acceptance | 根据验收标准检查任务完成情况 |
| `/security-gate` | security check | 敏感操作前的红线安全审查 |
| `/generate-help` | generate help | 根据当前配置生成 docs/help.html |
| `/export-prompt-log` | 导出提示词、export log | 按月导出提示词推理记录到 exports/ |

---

## 任务工作流

每个任务必须遵循以下流程：

```
1. 语义理解        →  解析用户输入，输出理解模板

2. 任务初始化      →  通过 /task-init 生成任务确认单

3. 执行阶段        →  在确认范围内执行，遵守护栏规则

4. 任务交接        →  通过 /context-handoff 压缩保存上下文
```

### 开发流程

**功能开发：**
```
产品岗（需求）→ 前端/后端（设计与编码）→ 测试岗（测试）→ 运维岗（部署）→ 验收岗（验收）
```

**热修复：**
```
确认问题 → 前端/后端（最小修复）→ 测试岗（快速验证）→ 运维岗（紧急部署）
```

**发版：**
```
发版分支 → 测试岗（全量回归）→ 仅修复缺陷 → 验收岗（签收）→ 运维岗（部署并打标签）
```

---

## 安全护栏

工作流强制执行严格的安全规则，**不可绕过**：

### 红线规则

| 类别 | 核心规则 |
|------|---------|
| **数据库** | 禁止访问生产数据库；禁止 DELETE/DROP/TRUNCATE；仅允许软删除；迁移需审批 |
| **Git** | 禁止对保护分支强制推送；禁止 --no-verify；必须使用 Conventional Commits |
| **数据** | 禁止在输出中暴露生产密钥/令牌；禁止暴露真实个人信息；分享给 AI 的代码必须脱敏 |
| **执行** | 未经确认禁止执行破坏性命令；未经审批禁止变更依赖；范围扩大必须重新运行 /task-init |

### 执行边界

| 任务类型 | 允许 | 禁止 |
|---------|------|------|
| 功能开发 | 在范围内创建/修改，添加测试 | 修改范围外代码，变更基础设施 |
| 缺陷修复 | 修复缺陷文件，添加回归测试 | 重构，修改无关代码 |
| 重构 | 重命名、提取、重组结构 | 改变行为，修改 API 契约 |
| 分析 | 只读，生成报告 | 修改任何文件 |
| 部署 | 部署脚本与配置 | 修改源代码 |

---

## 知识库

知识库是**核心的 token 节省机制**：

- **上下文压缩**：用 200-400 个 token 替代 3000-5000 个 token 的对话回放（节省 70-80%）
- **跨会话延续**：任务上下文在 Claude Code 会话间持久保存
- **跨角色交接**：产品岗上下文可无缝流转至开发、测试、运维和验收岗

### 结构

```
knowledge-base/
├── INDEX.md                    # 主索引（始终首先查阅）
├── contexts/
│   ├── active/                 # 进行中的任务上下文
│   └── archived/               # 已完成的任务上下文
├── prompts/                    # 可复用的提示词模板
│   ├── pm/                     # 产品岗模板（PRD、用户故事等）
│   ├── frontend/               # 前端模板
│   ├── backend/                # 后端模板（API 设计等）
│   ├── qa/                     # 测试岗模板（测试计划、缺陷报告）
│   ├── devops/                 # 运维岗模板（部署检查清单）
│   └── acceptance/             # 验收岗模板
├── prompt-logs/                # 提示词推理记录（由 /context-handoff 自动生成）
├── exports/                    # 导出文件（由 /export-prompt-log 生成）
└── decisions/                  # 架构决策记录（ADR）
```

---

## 自定义扩展

### 添加自定义角色

1. 按照角色模板创建 `docs/rules/roles/{role-id}.md`
2. 在 `ROLES.md` 中添加该角色（角色矩阵、权限矩阵、技能映射）
3. 更新 `CLAUDE.md` 中的角色路由部分

### 添加自定义技能

1. 创建 `.claude/skills/{skill-name}/SKILL.md`，包含 frontmatter
2. 在 `description` 字段中添加触发词
3. 在 `CLAUDE.md` 的可用技能表中注册

### 添加提示词模板

使用 `/knowledge-sync` 或手动在 `knowledge-base/prompts/{role}/` 中创建文件，格式如下：

```markdown
---
name: template-name
role: role-id
version: 1.0
last-updated: YYYY-MM-DD
description: 一行描述
---

# 模板标题

## 适用场景
何时使用此模板

## 模板内容
包含 {占位符} 的实际提示词模板

## 使用示例
一个填写完成的示例
```

---

## 设计理念

### 为什么需要这个工作流？

1. **防幻觉** — 强制语义理解步骤，防止 AI 猜测或捏造需求
2. **范围控制** — 任务边界防止范围蔓延；每次扩展都需要重新确认
3. **安全优先** — 红线规则不可绕过，即使用户明确要求也不行
4. **Token 高效** — 知识库压缩跨会话节省 70-80% 上下文 token
5. **角色隔离** — RBAC 阻止产品岗修改代码、阻止开发岗执行部署等
6. **可追溯** — 每个决策、变更和交接都有记录，可审计

### 架构原则

- **约定优于配置** — 合理的默认值，仅自定义差异部分
- **结构与内容分离** — 技能生成框架；用户填写项目特定内容
- **Claude Code 原生** — 完全基于 Claude Code 原生能力构建（rules、skills、CLAUDE.md）
- **无外部依赖** — 纯 Markdown 文件，无需构建工具，无运行时依赖

---

## 常见问题

**Q：这个工作流支持任意技术栈吗？**
A：是的。该工作流与技术栈无关。它生成的是基于角色的协作框架 —— 你只需在 CLAUDE.md 中填写你具体的技术栈信息即可。

**Q：可以使用更少的角色吗？**
A：完全可以。在设置过程中，取消选择不需要的角色即可。例如，独立开发者可能只需要后端开发 + 测试岗。

**Q：需要 Claude Code Pro 吗？**
A：此技能适用于任何支持自定义技能的 Claude Code 方案。

**Q：生成后如何更新？**
A：直接编辑任何生成的文件即可。使用 `/knowledge-sync` 更新知识库。重新运行 `/create-workflow-skill` 可以重新生成（会显示差异并在覆盖前询问确认）。

**Q：多人可以在同一仓库中使用吗？**
A：可以。角色系统是按会话分配的 —— 每个 Claude Code 会话选择一个角色。知识库（上下文、决策）是共享的，用于跨角色协作。

**Q：这个工作流只支持中文吗？**
A：不是。在设置向导中可以选择中文、English 或双语模式。

---

## 环境要求

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI 或 IDE 扩展
- 一个用于生成工作流的项目目录

---

## 贡献

欢迎贡献！请按以下步骤操作：

1. Fork 此仓库
2. 创建功能分支（`git checkout -b feature/amazing-feature`）
3. 提交你的更改（`git commit -m 'feat: add amazing feature'`）
4. 推送到分支（`git push origin feature/amazing-feature`）
5. 发起 Pull Request

---

## 许可证

本项目基于 MIT 许可证授权 - 详情请查看 [LICENSE](LICENSE) 文件。

---

## 致谢

- 为 Anthropic 的 [Claude Code](https://docs.anthropic.com/en/docs/claude-code) 构建
- 灵感来源于真实的多角色 AI 开发协作工作流实践
- 感谢社区贡献与反馈

---

**如果觉得有用，请给个 Star！**
