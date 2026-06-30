# Step 4: ROLES.md 模板

生成目标文件：`{project-root}/ROLES.md`

根据用户选择的角色动态生成。仅在矩阵中包含用户选择的角色和技能。

---

## 生成内容

# 角色注册表 / Role Registry

## 角色矩阵 / Role Matrix

| 角色ID | 中文名 | English | 上下文文件 | 核心技能 |
|--------|--------|---------|-----------|---------|
| pm | 产品岗 | Product Manager | docs/rules/roles/pm.md | task-init, acceptance-check, knowledge-sync |
| fe | 前端开发 | Frontend Dev | docs/rules/roles/frontend.md | task-init, context-handoff |
| be | 后端开发 | Backend Dev | docs/rules/roles/backend.md | task-init, context-handoff, security-gate |
| qa | 测试岗 | QA/Tester | docs/rules/roles/qa.md | task-init, acceptance-check |
| ops | 运维岗 | DevOps | docs/rules/roles/devops.md | task-init, security-gate |
| acc | 验收岗 | Acceptance | docs/rules/roles/acceptance.md | acceptance-check |

## 权限矩阵 / Permission Matrix

| 操作 | PM | FE | BE | QA | OPS | ACC |
|------|----|----|----|----|-----|-----|
| 读取源码 | Y | Y | Y | Y | Y | Y |
| 修改源码 | - | Y | Y | - | - | - |
| 运行构建 | - | Y | Y | - | Y | - |
| 运行测试 | - | Y | Y | Y | - | - |
| 数据库操作 | - | - | Y | - | - | - |
| 部署操作 | - | - | - | - | Y | - |
| 管理知识库 | Y | Y | Y | Y | Y | Y |
| 验收签字 | Y | - | - | Y | - | Y |

## 技能映射 / Skill Mapping

| 技能 | PM | FE | BE | QA | OPS | ACC |
|------|----|----|----|----|-----|-----|
| /task-init | Y | Y | Y | Y | Y | Y |
| /context-handoff | Y | Y | Y | Y | Y | - |
| /knowledge-sync | Y | - | - | - | - | - |
| /acceptance-check | Y | - | - | Y | - | Y |
| /security-gate | - | - | Y | - | Y | - |
| /generate-help | Y | - | - | - | Y | - |

---

## 动态生成说明

- 仅在矩阵中包含用户选择的角色列
- 仅在技能映射中包含用户选择的技能行
- 自定义角色按用户提供的信息添加到矩阵中
