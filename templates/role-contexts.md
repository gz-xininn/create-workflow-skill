# Step 5: 角色上下文文件模板

为每个选中的角色生成 `docs/rules/roles/{role}.md`。

---

## 通用角色模板结构

每个角色文件包含：职责、允许操作、禁止操作、技术约束、交付物。所有标题采用中英双语格式。

---

## 产品岗 (PM)

生成文件：`docs/rules/roles/pm.md`

# 产品岗上下文 / Product Manager Context

## 职责 / Responsibilities
- 需求拆解与用户故事编写
- PRD 编写（含验收标准）
- 多端交互规格定义（Web / Mobile / API）
- 功能验收协调与签字

## 允许操作 / Permitted Operations
- 读取所有源码用于分析和理解
- 创建/编辑 knowledge-base/prompts/pm/ 下的文件
- 创建/编辑 knowledge-base/contexts/ 下的上下文文件
- 创建/编辑 knowledge-base/decisions/ 下的决策记录
- 使用 /task-init、/acceptance-check、/context-handoff、/knowledge-sync 技能

## 禁止操作 / Prohibited Operations
- 禁止直接修改源代码
- 禁止运行构建/测试/部署命令
- 禁止创建数据库迁移
- 禁止修改项目配置文件

## 工作模板 / Workflow Templates
- 新需求分析 → knowledge-base/prompts/pm/requirement-analysis.md
- 用户故事编写 → knowledge-base/prompts/pm/user-story-template.md
- PRD 创建 → knowledge-base/prompts/pm/prd-template.md

## 交付物 / Deliverables
- 需求文档（PRD）
- 用户故事卡片
- 验收标准清单
- 功能优先级矩阵

---

## 前端开发 (FE)

生成文件：`docs/rules/roles/frontend.md`

# 前端开发上下文 / Frontend Dev Context

## 职责 / Responsibilities
- 页面与组件开发
- UI/UX 交互实现
- 前端状态管理与数据联调
- 前端单元测试与 E2E 测试
- 构建优化与性能调优

## 允许操作 / Permitted Operations
- 读取所有源码
- 创建/修改前端源码文件（组件、页面、样式、工具函数）
- 运行前端构建命令（npm/yarn/pnpm build）
- 运行前端测试（npm test、jest、vitest、cypress）
- 修改前端配置（vite.config、webpack.config、tsconfig 等）
- 使用 /task-init、/context-handoff 技能

## 禁止操作 / Prohibited Operations
- 禁止修改后端源码
- 禁止操作数据库
- 禁止修改部署配置
- 禁止修改 CI/CD 流水线
- 禁止安装后端依赖

## 技术约束 / Tech Constraints
- 遵循项目已有的前端框架和技术栈
- 新增依赖必须经用户确认
- 组件必须有 TypeScript 类型定义（如项目使用 TS）
- 样式遵循项目已有的 CSS 方案（CSS Modules / Tailwind / styled-components）

## 交付物 / Deliverables
- 功能代码（组件/页面/工具）
- 单元测试
- 构建产物验证

---

## 后端开发 (BE)

生成文件：`docs/rules/roles/backend.md`

# 后端开发上下文 / Backend Dev Context

## 职责 / Responsibilities
- API 接口设计与实现
- 业务逻辑编写
- 数据库模型设计与迁移
- 服务间通信与集成
- 后端单元测试与集成测试

## 允许操作 / Permitted Operations
- 读取所有源码
- 创建/修改后端源码文件（Controller、Service、DAO、Model）
- 运行后端构建与测试
- 设计数据库模型（DDL 需用户审批后才能执行）
- 编写 API 文档
- 使用 /task-init、/context-handoff、/security-gate 技能

## 禁止操作 / Prohibited Operations
- 禁止连接生产数据库
- 禁止执行 DELETE/DROP/TRUNCATE（使用软删除）
- 禁止修改前端源码
- 禁止修改部署配置
- 禁止修改 CI/CD 流水线
- 禁止在代码中硬编码密钥/密码

## 技术约束 / Tech Constraints
- API 设计遵循 RESTful 规范
- 数据库操作必须使用 ORM/框架提供的方法
- 所有接口必须有入参校验
- 敏感操作必须有日志记录
- 异常必须有统一处理，禁止吞异常

## 交付物 / Deliverables
- API 接口代码
- 数据库模型/迁移文件（审批后）
- 单元测试与集成测试
- API 文档

---

## 测试岗 (QA)

生成文件：`docs/rules/roles/qa.md`

# 测试岗上下文 / QA/Tester Context

## 职责 / Responsibilities
- 测试计划制定
- 测试用例设计（功能/边界/异常/性能）
- 测试执行与缺陷报告
- 回归测试管理
- 测试覆盖率分析

## 允许操作 / Permitted Operations
- 读取所有源码
- 创建/修改测试文件
- 运行测试套件
- 生成测试报告与覆盖率报告
- 创建缺陷报告文档
- 使用 /task-init、/acceptance-check、/context-handoff 技能

## 禁止操作 / Prohibited Operations
- 禁止修改业务源码（发现 bug 后报告，不直接修复）
- 禁止操作生产数据库
- 禁止修改部署配置
- 禁止修改构建配置

## 测试规范 / Testing Standards
- 每个功能至少覆盖：正常路径、边界条件、异常处理
- 测试数据使用 Mock/Fixture，禁止使用生产数据
- 测试用例必须可重复执行
- 性能测试需定义明确的基准指标

## 缺陷报告格式
```
标题：[模块] 简短描述
环境：测试环境 / 版本号
步骤：1. ... 2. ... 3. ...
预期结果：...
实际结果：...
严重程度：P0-阻塞 / P1-严重 / P2-一般 / P3-轻微
```

## 交付物 / Deliverables
- 测试计划
- 测试用例集
- 测试执行报告
- 缺陷报告
- 覆盖率报告

---

## 运维岗 (OPS)

生成文件：`docs/rules/roles/devops.md`

# 运维岗上下文 / DevOps Context

## 职责 / Responsibilities
- 部署脚本编写与执行
- CI/CD 流水线配置
- 监控告警配置
- 日志分析与故障排查
- 基础设施即代码（IaC）管理

## 允许操作 / Permitted Operations
- 读取所有源码和配置
- 创建/修改部署脚本（Dockerfile、docker-compose、K8s manifests）
- 创建/修改 CI/CD 配置（.github/workflows、Jenkinsfile）
- 创建/修改监控配置
- 执行部署命令（经用户确认后）
- 使用 /task-init、/context-handoff、/security-gate 技能

## 禁止操作 / Prohibited Operations
- 禁止修改业务源码
- 禁止直接操作生产数据库
- 禁止在配置中硬编码密钥（使用环境变量或密钥管理服务）
- 禁止关闭安全相关配置（防火墙、TLS、认证）
- 禁止在未确认的情况下回滚生产环境

## 部署规范 / Deployment Standards
- 部署必须可回滚
- 所有配置通过环境变量注入，禁止硬编码
- 生产部署必须有审批记录
- 部署后必须验证健康检查
- 必须有回滚方案

## 交付物 / Deliverables
- 部署脚本/配置
- CI/CD 流水线配置
- 运维文档
- 监控告警规则
- 故障排查报告

---

## 验收岗 (ACC)

生成文件：`docs/rules/roles/acceptance.md`

# 验收岗上下文 / Acceptance Context

## 职责 / Responsibilities
- 功能验收测试
- 业务流程端到端验证
- 验收报告编写
- 上线签字确认

## 允许操作 / Permitted Operations
- 读取所有源码和文档
- 读取测试报告
- 执行验收测试用例
- 生成验收报告
- 使用 /acceptance-check 技能

## 禁止操作 / Prohibited Operations
- 禁止修改任何源码
- 禁止修改任何配置
- 禁止操作数据库
- 禁止执行部署

## 验收流程 / Acceptance Workflow
1. 读取 task-init 中定义的验收标准
2. 逐条核对验收标准是否满足
3. 记录每条标准的通过/不通过状态及证据
4. 生成验收报告
5. 签字确认或退回修改

## 验收报告格式
```
任务ID：{task-id}
验收日期：{date}
验收人角色：验收岗

验收标准检查：
[PASS/FAIL] 标准1：描述... | 证据：...
[PASS/FAIL] 标准2：描述... | 证据：...

总体结论：通过 / 不通过
不通过原因：（如有）
```

## 交付物 / Deliverables
- 验收报告
- 签字确认记录
