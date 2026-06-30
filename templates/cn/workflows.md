# Step 7: 工作流程文件模板

生成 3 个文件到 `.claude/rules/workflows/` 目录。

---

## 文件 1: feature-dev.md

生成文件：`.claude/rules/workflows/feature-dev.md`

# 功能开发全流程

## 流程概览

    产品岗 → 前端/后端开发 → 测试岗 → 运维岗 → 验收岗
     (PM)      (FE/BE)       (QA)     (OPS)     (ACC)

## 阶段详情

### Phase 1: 需求定义（产品岗）
1. 运行 /task-init 选择角色 PM，任务类型 Feature
2. 编写需求文档（PRD）使用 knowledge-base/prompts/pm/prd-template.md
3. 定义验收标准
4. 运行 /context-handoff 保存需求上下文

### Phase 2: 技术方案（前端/后端开发）
1. 加载 Phase 1 的上下文
2. 运行 /task-init 选择对应开发角色
3. 编写技术方案
4. 如涉及架构决策，创建 ADR 记录到 knowledge-base/decisions/

### Phase 3: 编码实现（前端/后端开发）
1. 按技术方案实现功能
2. 编写单元测试
3. 提交 PR 并通过 Code Review
4. 运行 /context-handoff 保存开发上下文

### Phase 4: 测试验证（测试岗）
1. 加载 Phase 1 和 Phase 3 的上下文
2. 运行 /task-init 选择角色 QA
3. 编写测试用例
4. 执行测试并生成报告
5. 发现缺陷则创建 BugFix 任务，回到 Phase 3
6. 测试通过后运行 /context-handoff

### Phase 5: 部署上线（运维岗）
1. 加载所有相关上下文
2. 运行 /task-init 选择角色 OPS
3. 运行 /security-gate 执行安全审查
4. 执行部署
5. 验证健康检查
6. 运行 /context-handoff

### Phase 6: 验收签字（验收岗）
1. 加载所有上下文
2. 运行 /acceptance-check
3. 逐条核对验收标准
4. 签字确认或退回

---

## 文件 2: hotfix.md

生成文件：`.claude/rules/workflows/hotfix.md`

# 紧急修复流程

## 适用场景
生产环境出现紧急缺陷，需要快速修复并上线。

## 流程

### 1. 问题确认
- 运行 /task-init，任务类型选择 BugFix，优先级 P0
- 明确缺陷现象、影响范围、复现步骤

### 2. 修复实施（后端/前端开发）
- 从 main 创建 hotfix 分支
- 最小范围修复，禁止附带重构
- 必须添加回归测试用例

### 3. 快速验证（测试岗）
- 验证修复有效性
- 执行核心回归测试
- 确认无新增问题

### 4. 紧急部署（运维岗）
- 运行 /security-gate
- 部署 hotfix 分支
- 验证线上修复效果
- 准备回滚方案

### 5. 收尾
- 合并 hotfix 到 main 和 develop
- 打 tag
- 运行 /context-handoff 记录修复细节
- 创建事后复盘文档

## 时间要求
- P0 缺陷：发现后 4 小时内完成修复上线
- 修复期间跳过非必要审批流程，但 /security-gate 不可跳过

---

## 文件 3: release.md

生成文件：`.claude/rules/workflows/release.md`

# 发布流程

## 流程

### 1. 发布准备
- 从 develop 创建 release/{version} 分支
- 冻结新功能合入
- 运行 /task-init，任务类型选择 Release

### 2. 发布测试（测试岗）
- 执行全量回归测试
- 执行性能测试
- 执行安全扫描
- 生成测试报告

### 3. 缺陷修复（仅 BugFix）
- release 分支仅接受 bugfix 提交
- 禁止功能变更和重构
- 每个修复都需要回归测试

### 4. 发布审批
- 运行 /acceptance-check
- 核对所有验收标准
- 获取相关干系人签字

### 5. 执行发布（运维岗）
- 运行 /security-gate
- 合并 release 到 main
- 打版本 tag
- 执行生产部署
- 验证健康检查

### 6. 收尾
- 合并 release 回 develop
- 删除 release 分支
- 更新版本日志（CHANGELOG）
- 运行 /context-handoff 归档发布上下文
