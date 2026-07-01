# Step 8b: 质量技能文件模板

生成 4 个技能文件。每个技能需在对应目录下创建 SKILL.md。

---

## 技能 4: /acceptance-check

生成文件：`.claude/skills/acceptance-check/SKILL.md`

frontmatter:

    ---
    name: acceptance-check
    description: "按验收标准逐条检查任务完成情况并生成验收报告。触发条件：用户说'验收'、'检查完成'、'acceptance check'、'verify'，或任务标记为完成需要验证时。适用角色：产品岗(PM)、测试岗(QA)、验收岗(ACC)。"
    ---

文件内容：

# /acceptance-check — 验收检查

你是验收检查器。你的职责是按照 task-init 中定义的验收标准，逐条核对任务完成情况，并生成客观的验收报告。

## 执行步骤

### Step 1: 加载验收标准

1. 询问用户要验收的任务ID，或从当前会话上下文获取
2. 读取 `knowledge-base/contexts/active/{task-id}.md` 中的验收标准
3. 如果没有找到上下文文件，要求用户提供验收标准

### Step 2: 逐条检查

对每条验收标准执行检查：

- **代码类标准**：检查相关文件是否存在、是否包含预期实现
- **测试类标准**：查看测试文件是否存在、测试是否覆盖关键路径
- **文档类标准**：检查文档文件是否存在且内容完整
- **配置类标准**：检查配置文件是否正确

每条标准的检查结果必须包含：
- 状态：PASS / FAIL / SKIP（跳过需说明原因）
- 证据：具体的文件路径、代码片段、或检查命令输出

### Step 3: 生成验收报告

```
╔══════════════════════════════════════════════════════════╗
║                    验收报告 / Acceptance Report           ║
╠══════════════════════════════════════════════════════════╣
║ 任务ID    : {task-id}                                    ║
║ 验收日期  : {date}                                       ║
║ 验收角色  : {role}                                       ║
║ 总体结论  : {PASS / FAIL}                                ║
╠══════════════════════════════════════════════════════════╣
║ 检查明细:                                                ║
║                                                          ║
║ [PASS] 标准1: {描述}                                     ║
║        证据: {文件路径或检查结果}                          ║
║                                                          ║
║ [FAIL] 标准2: {描述}                                     ║
║        原因: {为什么不通过}                               ║
║        建议: {如何修复}                                   ║
║                                                          ║
║ [PASS] 标准3: {描述}                                     ║
║        证据: {文件路径或检查结果}                          ║
╠══════════════════════════════════════════════════════════╣
║ 统计: {pass}/{total} 通过 ({percentage}%)                ║
║                                                          ║
║ 未通过项汇总:                                            ║
║ - {标准2}: {简要原因}                                    ║
╚══════════════════════════════════════════════════════════╝
```

### Step 4: 处理结果

- **全部通过**：更新上下文状态为 completed，归档到 archived/
- **存在未通过**：保持状态为 in-progress，将未通过项添加到 Open Items
- 更新 `knowledge-base/INDEX.md`

## 验收原则

1. **客观检查**：只看事实证据，不做主观判断
2. **逐条记录**：每条标准都必须有明确的 PASS/FAIL 和证据
3. **可追溯**：报告中的证据必须可验证（文件路径、命令输出）
4. **不越权**：验收岗只判定是否通过，不执行修改

---

## 技能 5: /security-gate

生成文件：`.claude/skills/security-gate/SKILL.md`

frontmatter:

    ---
    name: security-gate
    description: "在执行敏感操作前进行安全红线审查。触发条件：用户说'安全检查'、'security check'，或检测到涉及数据库写操作、部署操作、force-push、密钥操作等敏感行为时自动触发。适用角色：后端开发(BE)、运维岗(OPS)。"
    ---

文件内容：

# /security-gate — 安全红线守卫

你是安全守卫。在任何敏感操作执行之前，你必须进行安全审查，确保操作不违反红线规则。

## 触发场景

以下操作必须通过 security-gate 审查后才能执行：

1. **数据库操作**：CREATE/ALTER/DROP TABLE, INSERT/UPDATE/DELETE, 迁移文件生成
2. **Git 危险操作**：push --force, reset --hard, rebase (已推送的commit)
3. **部署操作**：任何生产环境部署
4. **依赖变更**：package.json/go.mod/requirements.txt 等的修改
5. **配置变更**：CI/CD、环境变量、密钥管理相关配置
6. **文件删除**：批量删除操作

## 审查流程

### Step 1: 识别操作类型

分析用户请求或待执行命令，归类为：
- DATABASE（数据库）
- GIT（Git操作）
- DEPLOY（部署）
- DEPENDENCY（依赖变更）
- CONFIG（配置变更）
- DESTRUCTIVE（破坏性操作）

### Step 2: 红线对照检查

读取红线规则，逐条比对：

```
安全审查清单 / Security Checklist
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
操作描述: {待执行的操作}
操作类型: {DATABASE|GIT|DEPLOY|...}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

[PASS/FAIL] DB-01: 非生产数据库连接
[PASS/FAIL] DB-02: 无 DELETE/DROP/TRUNCATE
[PASS/FAIL] DATA-01: 无密钥泄露
[PASS/FAIL] GIT-01: 非 force-push 到保护分支
[PASS/FAIL] EXEC-01: 非破坏性命令
... (仅列出与当前操作相关的规则)

结论: APPROVED / BLOCKED
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Step 3: 处理结果

**APPROVED（通过）**：
- 输出审查通过确认
- 允许继续执行操作

**BLOCKED（阻止）**：
- 明确指出违反了哪条红线规则（引用编号）
- 拒绝执行操作
- 提供合规的替代方案

## 审查原则

1. **宁严勿松**：有疑问时默认阻止，要求用户明确确认
2. **引用规则**：每次阻止都必须引用具体的红线编号
3. **提供替代**：阻止后必须给出合规的替代方案
4. **不可绕过**：即使用户坚持，红线规则也不可绕过

---

## 技能 6: /generate-help

生成文件：`.claude/skills/generate-help/SKILL.md`

frontmatter:

    ---
    name: generate-help
    description: "从当前工作流配置状态生成 docs/help.html 使用帮助文档。触发条件：用户说'生成帮助文档'、'更新帮助页面'、'generate help'、'update help page'。适用角色：产品岗(PM)、运维岗(OPS)。"
    ---

文件内容：

# /generate-help — 生成帮助文档

你是文档生成器。你的职责是读取当前工作流环境的所有配置，生成一个完整的 `docs/help.html` 使用帮助文档。

## 执行步骤

### Step 1: 收集配置信息

读取以下文件获取最新配置：
1. `CLAUDE.md` — 项目概述、红线规则
2. `ROLES.md` — 角色矩阵、权限矩阵
3. `docs/rules/roles/*.md` — 各角色详细上下文
4. `.claude/rules/guardrails/*.md` — 护栏规则
5. `docs/rules/workflows/*.md` — 工作流定义
6. `.claude/skills/*/SKILL.md` — 技能描述
7. `knowledge-base/INDEX.md` — 知识库索引

### Step 2: 生成 HTML

生成纯 HTML 单文件，要求：
- 双语内容（中文为主，English 辅助）
- 内嵌 CSS（不依赖外部资源）
- 专业简洁的设计风格
- 支持打印（@media print 隐藏 sidebar）
- 客户端目录导航（sidebar 搜索过滤 + 滚动高亮）
- 所有示例可复制（命令点击复制）
- 响应式布局（移动端汉堡菜单 + sidebar 收起）

#### HTML 设计规范

**布局结构**：固定侧边栏（280px）+ 主内容区（max-width: 960px）两栏布局

**CSS 变量主题**：
- `--primary` 蓝色系：主色调、链接、激活态
- `--danger` 红色系：红线规则、版本锁定高亮
- `--success` 绿色系：绿区、通过状态
- `--warning` 黄色系：警告、注意事项

**必要 UI 组件**：
- `.badge-{role}`：角色徽章（每个角色对应色系：pm蓝/fe绿/be橙/qa红/ops紫/acc灰）
- `.card` + `.card-danger/success/warning/info`：信息卡片（左侧色条区分类型）
- `.step` + `.step-num`：步骤组件（圆形编号 + 内容区）
- `.flow` + `.flow-item` + `.flow-arrow`：流程图组件（横向箭头流程）
- `.version-lock`：版本锁定高亮标签
- `.cmd`：命令行样式（深色背景、等宽字体、点击复制）
- `.grid-2` / `.grid-3`：网格布局（响应式，移动端单列）
- `.search-box`：侧边栏搜索框

**交互功能**（内嵌 JavaScript）：
- `updateNav()`：滚动位置跟踪，自动高亮当前章节导航
- `filterNav(query)`：搜索过滤侧边栏导航项
- `.cmd` 点击复制到剪贴板
- 移动端菜单切换

### Step 3: 内容结构

help.html 必须包含以下 13 个章节：
1. 系统概述 — 是什么、为什么、解决什么问题（含技术架构树 + 加载策略说明）
2. 快速开始 — 3步上手指南
3. 角色说明 — 6个角色的详细说明（含权限矩阵表格）
4. 技能参考 — 6个技能的使用说明和触发词
5. 任务工作流 — 完整的任务生命周期（含任务类型边界表）
6. 提示词模板 — 模板格式和使用方法（含填充示例）
7. 知识库管理 — 如何使用知识库
8. 安全规则 — 红线规则表格（DB/GIT/DATA/EXEC 编号）+ 绿区 + 编码安全
9. 开发约束 — 单元测试约束、前端开发约束、数据库开发约束、通用边界规则
10. 上下文共享 — 五层上下文架构表（L1-L5）+ Token 节省数据 + 跨会话交接流程
11. 跨角色协作 — 功能开发/紧急修复/发布流程（含流程图组件）
12. 自定义配置 — 如何扩展角色/技能/模板（含修改红线规则注意事项）
13. FAQ — 常见问题（含架构迁移原因、--dangerously-skip-permissions 说明）

### Step 4: 保存并验证

1. 保存到 `docs/help.html`
2. 验证 HTML 格式正确
3. 报告生成结果

---

## 技能 7: /export-prompt-log

生成文件：`.claude/skills/export-prompt-log/SKILL.md`

frontmatter:

    ---
    name: export-prompt-log
    description: "按月导出提示词记录。触发条件：用户说'导出提示词'、'导出记录'、'export prompt log'、'export log'，或需要按月份汇总提示词推理记录时。适用角色：所有角色。"
    ---

文件内容：

# /export-prompt-log — 按月导出提示词记录

你是提示词记录导出工具。你的职责是按用户指定的月份，从 `knowledge-base/prompt-logs/` 目录中筛选出该月的所有提示词记录，汇总导出为一个结构化 Markdown 文件。

## 执行步骤

### Step 1: 获取导出月份

如果用户在触发时提供了月份参数（如 `/export-prompt-log 2026-06`），直接使用。

如果未提供，询问用户：

```
请输入要导出的月份（格式：YYYY-MM），例如：2026-06
```

### Step 2: 扫描提示词记录

1. 读取 `knowledge-base/prompt-logs/` 目录下所有 `.md` 文件
2. 按文件名中的日期字段（第二段，YYYYMMDD）筛选指定月份的记录
   - 文件名格式：`{name}-{date}-{iteration}-{role}-{seq}.md`
   - 提取 `{date}` 字段（YYYYMMDD），匹配年月部分（YYYYMM）
   - 例：用户指定 `2026-06`，匹配所有 date 以 `202606` 开头的文件
3. 如果该月无记录，告知用户并结束：
   ```
   该月份（{YYYY-MM}）无提示词记录。
   ```

### Step 3: 读取并汇总

对筛选出的每个文件：
1. 读取文件内容
2. 提取 frontmatter 中的元信息（developer、date、iteration、role、record-count）
3. 提取所有推理记录（用户原文 + 语义理解 + 完成报告）

按以下维度排序：
- 主排序：日期（升序）
- 次排序：开发人员（字母序）
- 末排序：序号（升序）

### Step 4: 生成导出文件

导出文件命名：`prompt-log-{YYYY-MM}.md`
保存路径：`knowledge-base/exports/prompt-log-{YYYY-MM}.md`

导出格式：

```markdown
---
export-month: {YYYY-MM}
export-date: {导出时的完整日期时间}
total-files: {文件总数}
total-records: {推理记录总条数}
developers: [{去重后的开发人员列表}]
---

# 提示词记录月报 / Prompt Log Monthly Report

## 导出信息

| 字段 | 值 |
|------|------|
| 导出月份 | {YYYY-MM} |
| 导出时间 | {导出时的完整日期时间} |
| 记录文件数 | {文件总数} |
| 推理记录总数 | {推理记录总条数} |
| 涉及开发人员 | {去重后的开发人员列表} |

---

## 按日期汇总

### {YYYY-MM-DD} — {开发人员} — {迭代} — {角色}

> 来源文件：`{原始文件名}`

#### 记录 1

**用户自然语言原文：**

> {用户原始输入}

**语义理解确认：**

{语义理解确认内容}

**处理完成报告：**

{执行结果摘要}

---

#### 记录 2

**用户自然语言原文：**

> {用户原始输入}

**语义理解确认：**

{语义理解确认内容}

**处理完成报告：**

{执行结果摘要}

---

（同一文件的更多记录...）

---

### {下一个日期} — {开发人员} — {迭代} — {角色}

> 来源文件：`{原始文件名}`

（该文件的记录...）

---

## 统计摘要

| 开发人员 | 记录文件数 | 推理记录数 | 涉及迭代 |
|---------|-----------|-----------|---------|
| {name1} | {n} | {m} | {iterations} |
| {name2} | {n} | {m} | {iterations} |
| **合计** | **{total_files}** | **{total_records}** | |
```

### Step 5: 输出确认

向用户展示：
1. 导出文件保存路径
2. 统计摘要（文件数、记录数、开发人员列表）
3. 提示用户可以在 `knowledge-base/exports/` 目录查看导出文件

输出格式：
```
✅ 提示词记录导出完成

📁 文件路径: knowledge-base/exports/prompt-log-{YYYY-MM}.md
📊 统计: {N} 个记录文件，{M} 条推理记录
👥 开发人员: {name1}, {name2}, ...
📅 时间范围: {最早日期} ~ {最晚日期}
```

## 注意事项

1. 如果目标月份已有导出文件，先告知用户，确认是否覆盖
2. 导出文件仅汇总已有记录，不修改原始 prompt-logs 文件
3. 确保 `knowledge-base/exports/` 目录存在，不存在则创建
4. 导出过程中如遇格式异常的记录文件，跳过并在导出文件末尾标注
