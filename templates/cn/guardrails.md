# Step 6: 护栏规则文件模板

生成 3 个文件到 `.claude/rules/guardrails/` 目录。

---

## 文件 1: boundaries.md

生成文件：`.claude/rules/guardrails/boundaries.md`

# 执行边界定义

## 任务类型与边界

每种任务类型有明确的执行边界，超出边界必须停止并重新确认。

### Feature（新功能开发）
- 范围：task-init 中确认的模块和文件
- 允许：创建新文件、修改确认范围内的文件、添加测试
- 禁止：修改范围外文件、修改公共基础设施、修改构建配置

### BugFix（缺陷修复）
- 范围：仅限缺陷相关的文件
- 允许：修改缺陷所在文件、添加回归测试
- 禁止：顺便重构、修改无关代码、修改 API 签名

### Refactor（重构）
- 范围：task-init 中确认的重构目标
- 允许：重命名、提取方法/类、调整目录结构
- 禁止：改变外部行为、修改 API 契约、删除功能

### Analysis（分析）
- 范围：只读分析，不修改任何文件
- 允许：读取源码、生成分析报告、输出建议
- 禁止：修改任何文件、执行任何写操作

### Deploy（部署）
- 范围：仅限运维岗操作
- 允许：生成部署脚本、修改部署配置、执行部署命令
- 禁止：修改源码、修改业务逻辑

### Test（测试）
- 范围：测试文件和测试配置
- 允许：创建/修改测试文件、运行测试、生成测试报告
- 禁止：修改被测源码（除非测试发现的 bug 经确认后修复）

### Review（评审）
- 范围：只读评审，输出评审意见
- 允许：读取代码、生成评审报告
- 禁止：直接修改代码

### Docs（文档）
- 范围：文档文件
- 允许：创建/修改文档、生成 API 文档
- 禁止：修改源码

## 通用边界规则

1. **单次任务文件数限制**：修改超过 10 个文件时必须分拆任务
2. **单文件修改行数限制**：单个文件修改超过 200 行时需要用户确认
3. **范围蔓延检测**：执行过程中如果发现需要修改范围外文件，立即停止并告知用户
4. **依赖变更隔离**：package.json / go.mod / requirements.txt 等依赖文件的修改必须单独确认

---

## 文件 2: git-workflow.md

生成文件：`.claude/rules/guardrails/git-workflow.md`

# Git 工作流规范

## 分支命名规范

    main                        # 主分支，保护分支
    develop                     # 开发分支
    feature/{role}-{desc}       # 功能分支，例：feature/fe-user-profile
    bugfix/{role}-{desc}        # 缺陷修复，例：bugfix/be-login-error
    hotfix/{desc}               # 紧急修复
    release/{version}           # 发布分支

## Commit 规范（Conventional Commits）

格式：`type(scope): description`

### type 取值

| type | 说明 | 示例 |
|------|------|------|
| feat | 新功能 | feat(user): add profile page |
| fix | 缺陷修复 | fix(auth): resolve token expiry issue |
| docs | 文档 | docs(api): update endpoint documentation |
| style | 格式 | style(ui): fix indentation |
| refactor | 重构 | refactor(service): extract base class |
| test | 测试 | test(user): add profile unit tests |
| chore | 构建/工具 | chore(deps): upgrade lodash |
| ci | CI配置 | ci(github): add lint workflow |

### scope 建议值
- 按模块：user, auth, order, payment, config
- 按层级：api, service, dao, ui, middleware

## 工作流程

### 功能开发
1. 从 develop 创建 feature 分支
2. 开发完成后提交 PR 到 develop
3. Code Review 通过后合并
4. 删除 feature 分支

### 紧急修复
1. 从 main 创建 hotfix 分支
2. 修复并测试
3. 提交 PR 到 main 和 develop
4. 合并后打 tag

### 发布流程
1. 从 develop 创建 release 分支
2. 仅允许 bugfix 提交
3. 测试通过后合并到 main
4. 打 tag，合并回 develop

## 禁止操作

- 禁止直接向 main/master 提交代码
- 禁止 force-push 到任何共享分支
- 禁止 rebase 已推送的 commit
- 禁止在 commit message 中包含敏感信息

---

## 文件 3: redlines.md

生成文件：`.claude/rules/guardrails/redlines.md`

# 红线规则详细定义

本文件是 CLAUDE.md 中红线规则的完整展开版。敏感操作时由 security-gate 技能自动加载。

## 1. 数据库安全红线

| 编号 | 规则 | 违规后果 |
|------|------|---------|
| DB-01 | 禁止连接生产数据库 | 立即终止任务 |
| DB-02 | 禁止执行 DELETE / DROP / TRUNCATE 语句 | 立即终止任务 |
| DB-03 | 迁移文件必须经用户明确审批后才能生成 | 回退并等待审批 |
| DB-04 | 禁止在代码或对话中出现真实数据库凭证 | 脱敏处理 |
| DB-05 | 批量 UPDATE 必须带 WHERE 条件且先做 SELECT 确认 | 重新编写 |
| DB-06 | 必须使用软删除（update delete_time），禁止物理删除 | 重新编写 |
| DB-07 | 线上数据库严禁开放外部连接，禁止与生产环境数据结构、账号密码一致 | 立即终止 |

## 2. Git 安全红线

| 编号 | 规则 | 违规后果 |
|------|------|---------|
| GIT-01 | 禁止 force-push 到 main/master/release 分支 | 立即终止 |
| GIT-02 | 禁止 --no-verify 跳过 pre-commit hooks | 修复 hook 问题 |
| GIT-03 | 禁止 git reset --hard（除非用户明确要求） | 使用安全替代方案 |
| GIT-04 | Commit message 必须遵循 Conventional Commits | 重新提交 |
| GIT-05 | 禁止 amend 已推送的 commit | 创建新 commit |
| GIT-06 | 禁止删除远程分支（除非用户明确要求） | 确认后操作 |

## 3. 数据安全红线

| 编号 | 规则 | 违规后果 |
|------|------|---------|
| DATA-01 | 禁止输出生产环境密钥/Token/证书 | 立即脱敏 |
| DATA-02 | 禁止暴露真实客户 PII（姓名/手机/身份证/地址） | 使用模拟数据 |
| DATA-03 | 禁止将核心算法或商业逻辑传输至外部服务 | 拒绝操作 |
| DATA-04 | 禁止在对话中出现生产 URL/IP/端口映射 | 使用占位符 |
| DATA-05 | 与 AI 共享的代码必须脱敏（移除真实密钥/连接串/PII） | 脱敏后重试 |
| DATA-06 | 禁止将 .env 文件内容输出到对话或提交到 Git | 拒绝操作 |

## 4. 执行安全红线

| 编号 | 规则 | 违规后果 |
|------|------|---------|
| EXEC-01 | 禁止使用 `--dangerously-skip-permissions` 等自动化指令跳过权限确认 | 立即终止 |
| EXEC-02 | 禁止 rm -rf / del /s /q 等破坏性命令（除非用户确认） | 拒绝执行 |
| EXEC-03 | 禁止安装/卸载包或修改依赖（除非用户批准） | 等待批准 |
| EXEC-04 | 影响超过 3 个文件的变更必须先列出文件清单并确认 | 先确认再执行 |
| EXEC-05 | 禁止启动浏览器/构建进程、修改 CI/CD 配置（除非用户明确要求） | 等待指令 |
| EXEC-06 | 任务范围不可擅自扩大，需要扩大时重新运行 /task-init | 回退到 task-init |
