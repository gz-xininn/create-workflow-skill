# Step 10: settings.json Template

Target file: `{project-root}/.claude/settings.json`

---

## Generated Content

Generate the following JSON configuration file:

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

## Notes

- The allow list contains permissions for read-only operations
- The deny list contains dangerous operations corresponding to red-line rules (covering both Bash and PowerShell tools)
- On Windows, Claude Code provides both Bash (Git Bash) and PowerShell tools, so deny rules must be set for both
- This configuration file is the target project's .claude/settings.json, not this skill's settings.json
