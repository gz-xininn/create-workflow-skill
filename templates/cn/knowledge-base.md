# Step 9: 知识库结构模板

生成知识库目录结构和初始文件。

---

## 目录结构

    knowledge-base/
    ├── INDEX.md
    ├── contexts/
    │   ├── active/
    │   └── archived/
    ├── prompts/
    │   ├── pm/
    │   ├── frontend/
    │   ├── backend/
    │   ├── qa/
    │   ├── devops/
    │   └── acceptance/
    ├── prompt-logs/
    ├── exports/
    └── decisions/

注意：prompts/ 下仅创建用户选择的角色对应的子目录。

---

## 文件 1: INDEX.md

生成文件：`knowledge-base/INDEX.md`

```markdown
# 知识库索引 / Knowledge Base Index

## 进行中的上下文 / Active Contexts

_暂无活跃任务_

## 已归档的上下文 / Archived Contexts

_暂无归档任务_

## 提示词模板 / Prompt Templates

> 使用 /knowledge-sync 添加你的项目提示词模板

## 提示词记录 / Prompt Logs

_由 /context-handoff 自动生成，记录每次会话的提示词推理过程_

## 导出记录 / Exported Logs

_导出文件由 /export-prompt-log 生成，存放于 exports/ 目录_

## 架构决策 / Architecture Decisions

> 使用 /knowledge-sync 记录重要的架构决策
```

---

## 文件 2: ADR 模板

生成文件：`knowledge-base/decisions/ADR-001-template.md`

```markdown
---
id: ADR-001
status: proposed | accepted | deprecated
date: {日期}
decision-makers: {参与角色}
---

# ADR-001: {标题}

## 背景 / Context
{为什么需要做这个决策}

## 决策 / Decision
{决定了什么}

## 影响 / Consequences
{这个决策带来的影响}
```

---

## 文件 3: 上下文模板示例

供 /context-handoff 生成时参考的格式：

```markdown
---
task-id: {YYYY-MM-DD}-{short-title}
role: {角色ID}
status: completed | in-progress | blocked
created: {创建时间}
updated: {更新时间}
related: []
---

## 摘要 / Summary
{2-3 句话描述完成了什么}

## 决策记录 / Decisions
- {决策}: {原因}

## 变更清单 / Changes
- {文件路径}: {变更内容和原因}

## 未完成项 / Open Items
- [ ] {待办}: {说明}

## 关键引用 / Key References
- {文件或知识库链接}: {为什么重要}

## 下一步 / Next Steps
- {建议的下一个操作}
- {交接给哪个角色}
```
