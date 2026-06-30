# Step 10: settings.json 模板

生成文件：`{project-root}/.claude/settings.json`

---

## 生成内容

生成以下 JSON 配置文件：

    {
      "permissions": {
        "allow": [
          "Read(**)",
          "Glob(**)",
          "Grep(**)",
          "Bash(ls *)",
          "Bash(git status)",
          "Bash(git status *)",
          "Bash(git log *)",
          "Bash(git diff *)",
          "Bash(git branch *)",
          "Bash(git show *)"
        ],
        "deny": [
          "Bash(rm -rf *)",
          "Bash(git push --force *)",
          "Bash(git push -f *)",
          "Bash(git reset --hard *)",
          "Bash(DROP TABLE *)",
          "Bash(DROP DATABASE *)",
          "Bash(DELETE FROM *)",
          "Bash(TRUNCATE *)",
          "Bash(*--dangerously*)",
          "Bash(*--no-verify*)",
          "PowerShell(Remove-Item * -Recurse *)",
          "PowerShell(Remove-Item * -Force *)",
          "PowerShell(del * -Recurse *)",
          "PowerShell(git push --force *)",
          "PowerShell(git push -f *)",
          "PowerShell(git reset --hard *)",
          "PowerShell(*DROP TABLE*)",
          "PowerShell(*DROP DATABASE*)",
          "PowerShell(*DELETE FROM*)",
          "PowerShell(*TRUNCATE*)",
          "PowerShell(*--dangerously*)",
          "PowerShell(*--no-verify*)"
        ]
      }
    }

## 说明

- allow 列表包含只读操作的权限
- deny 列表包含红线规则对应的危险操作（Bash + PowerShell 双工具覆盖）
- Windows 环境下 Claude Code 同时提供 Bash（Git Bash）和 PowerShell 工具，需要对两者都设置 deny 规则
- 此配置文件是目标项目的 .claude/settings.json，不是本技能的 settings.json
