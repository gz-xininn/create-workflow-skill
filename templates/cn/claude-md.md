# Step 3: CLAUDE.md 模板

生成目标文件：`{project-root}/CLAUDE.md`

根据用户选择的角色和技能动态生成。以下为完整模板，用 `{placeholder}` 标注需替换的内容。

---

## 生成内容

# {project-name}

基于 Claude Code 原生能力的多角色团队协作工作流环境。

## 项目概览 / Project Overview

**项目名称:** {project-name}
**源码根目录:** {project-root}

> 请在此处填写你的项目信息（技术栈、模块、端口等），建议以表格形式列出：
>
> | 项目 | 类型 | 技术栈 | 端口 |
> |------|------|--------|------|
> | {示例} | {类型} | {技术栈} | {端口} |

### 版本锁定（不可变更）/ Version Locks

> 在此记录项目中不可变更的依赖版本及原因。

| 项目 | 依赖 | 锁定版本 | 原因 |
|------|------|---------|------|
| {项目名} | {依赖名} | {版本} | {锁定原因} |

---

## 角色路由 / Role Routing

每次会话开始时，如果用户未声明角色，必须先询问：

> 请选择你的角色 / Please select your role:
> {根据 Step 1 选择的角色动态生成选项列表}

确认角色后，读取对应上下文文件 `docs/rules/roles/{role}.md`，严格遵守该角色的权限边界。
角色对照表详见 `ROLES.md`。

## 任务流程 / Task Workflow（强制执行）

每个任务必须遵循以下流程，不可跳过：

1. **语义理解** — 对用户的自然语言输入进行语义解析，输出「语义理解确认」模板（含迭代信息和任务清单），等待用户确认理解正确。禁止幻觉和自我编造，无法理解时直接提示：`没理解，请重新描述提示词`
2. **任务初始化** — 语义理解确认后，使用 `/task-init` 生成结构化任务确认单，等待用户确认后才开始执行
3. **执行阶段** — 严格在确认的范围内执行，遵守护栏规则
4. **任务交接** — 使用 `/context-handoff` 压缩上下文，存入 `knowledge-base/contexts/active/`

---

## 安全规则 / Security Rules（绝对禁止，不可覆盖）

* 禁止一切猜想和幻觉，所有执行必须有根有据

### 红区 — 数据库安全
- 禁止连接生产数据库，仅允许本地/测试数据库
- 线上数据库严禁开放外部连接，仅允许服务器本地访问
- 禁止与生产环境数据结构、账号密码一致
- 禁止执行 DELETE / DROP / TRUNCATE，必须使用软删除（update delete_time）
- 批量 UPDATE 必须带 WHERE 条件且先做 SELECT 确认
- 禁止直接生成 migration，必须先输出设计方案等待用户确认
- migration 完成后必须同步更新对应的模块文档
- 任何涉及数据库的任务，**必须先读数据库概览文档**
- 禁止输出真实连接串、密码、凭证

### 红区 — Git 安全
- 禁止 force-push 到 main/master/release 分支
- 禁止使用 --no-verify 跳过 pre-commit hooks
- 禁止 git reset --hard（除非用户明确要求）
- 禁止 amend 已推送的 commit，必须创建新 commit
- 禁止删除远程分支（除非用户明确要求）
- 必须使用 Conventional Commits 格式：`type(scope): description`
- 禁止在 commit message 中包含敏感信息

### 红区 — 数据安全
- 禁止输出生产环境的密钥、API Key、Token、证书
- 禁止暴露真实客户 PII 数据（姓名/手机/身份证/地址）
- 禁止将核心算法或商业逻辑传输至外部服务
- 禁止在对话中暴露生产 URL、IP 地址、端口映射
- 禁止将 .env 文件内容输出到对话或提交到 Git
- 与 AI 共享的代码片段必须脱敏（移除真实密钥、连接串、PII）
- 涉及敏感密钥不允许外传，使用要经过授权同意

### 红区 — 执行安全
- 禁止使用 `--dangerously-skip-permissions` 等自动化指令跳过权限确认
- 禁止执行破坏性操作（rm -rf、del /s /q、DROP TABLE 等）除非用户明确确认
- 禁止安装包或修改依赖除非用户批准
- 影响超过 3 个文件的变更必须先列出文件清单并确认
- 每个任务必须有明确范围，禁止擅自扩大范围
- 涉及敏感操作（数据库写入、文件删除、远程推送）必须先向用户确认再执行

### 绿区 — 允许操作
- 读取项目内任意源码用于分析
- 已脱敏的伪代码、通用算法思路讨论
- 去除敏感信息的报错日志、堆栈信息分析
- 通用工具类代码、正则表达式、SQL 转换
- 生成文档、注释、测试脚手架
- 读写 knowledge-base/ 目录
- 生成提示词模板和上下文摘要
- Git 只读操作：status、log、diff、branch

### 编码安全约束
- 避免在代码中硬编码敏感信息，使用环境变量管理配置
- 验证用户输入，防止 SQL 注入和 XSS 攻击
- 实施适当的权限控制

---

## 开发约束 / Development Constraints

> 在此添加项目特定的开发约束，例如：
> - 单元测试规则
> - 前端构建规则
> - API 调用规则

---

## 知识库 / Knowledge Base

开始任务时，先读取 `knowledge-base/INDEX.md` 检查是否有相关上下文。
如果找到，直接加载对应上下文文件，而不是让用户重新解释。
这是节约 token 的核心机制。

## 上下文加载顺序 / Context Loading Order

1. 本文件（始终加载）
2. 护栏规则 `.claude/rules/guardrails/`（始终加载，核心安全规则）
3. 角色上下文 `docs/rules/roles/{role}.md`（按需加载，确认角色后 Read 读取）
4. 任务上下文 `knowledge-base/contexts/active/{task-id}.md`（按需加载，如果存在）
5. 工作流规范 `docs/rules/workflows/`（按需加载，涉及跨角色协作时 Read 读取）

> **架构说明**：角色文件和工作流文件放在 `docs/rules/` 而非 `.claude/rules/`，因为 `.claude/rules/` 下的所有文件会被 Claude Code 自动加载为系统上下文。如果全部放在 `.claude/rules/`，每次 API 调用会携带过多系统提示 token，长对话时可能触发 API 错误。仅保留 3 个核心护栏文件自动加载，其余按需 Read 加载。

## 可用技能 / Available Skills

| 技能 | 触发词 | 用途 |
|------|--------|------|
| /task-init | 新任务、开始、start | 生成任务提示词模板 |
| /context-handoff | 保存上下文、交接、wrap up | 压缩并保存上下文 + 自动生成提示词记录 |
| /knowledge-sync | 同步知识库、添加模板 | 知识库增删改查 |
| /acceptance-check | 验收、检查完成 | 验收标准检查 |
| /security-gate | 安全检查 | 安全红线审查 |
| /generate-help | 生成帮助文档 | 生成 help.html |
| /export-prompt-log | 导出提示词、导出记录 | 按月导出提示词推理记录 |

---

## 动态生成说明

- 角色路由中的选项列表：仅列出用户选择的角色
- 可用技能表：仅列出用户选择的技能
- 安全规则：始终完整包含，不可裁剪
