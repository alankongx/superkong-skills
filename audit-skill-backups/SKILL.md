---
name: audit-skill-backups
description: |
  检查工作区中的 Skill 与珍藏库最新备份是否一致，并在需要时自动补做备份。
  当用户要求巡检 Skill 备份状态、查漏补缺、找出哪些 Skill 没备份或一键补备份时使用。
---

## 触发指令
- `检查一下哪些 Skill 没备份`
- `巡检 Skill 备份状态`
- `查一下哪些 skill 还没备份`
- `把没备份的都补上`
- `检查技能备份`
- `审计 Skill 备份`
- `skill 备份巡检`
- `备份巡检`

## 目标
- 扫描工作区下所有有效 Skill
- 对比珍藏库中的最新版本
- 判断：
  - 已与最新备份一致
  - 需要新备份
  - 备份异常
- 在需要时自动补做备份

## 工作区与备份库
- 工作区 Skill 根目录：`~/.openclaw/workspace/skills`
- 珍藏库根目录：`/Users/alan/Desktop/软件编程/龙虾相关/SKILL珍藏库`
- 底层脚本：`/Users/alan/bin/skill-backup`
- 巡检脚本：`/Users/alan/bin/skill-backup-audit`

## 执行步骤
1. 运行：
```bash
/Users/alan/bin/skill-backup-audit
```
2. 读取返回 JSON
3. 按 `status` 分类汇总：
   - `success`：本次已补做新备份
   - `skipped_same_as_latest`：当前工作区与最新备份一致
   - 其他：视为异常
4. 向用户汇报：
   - 总 Skill 数
   - 本次新备份数量
   - 已最新数量
   - 异常数量
   - 如有新备份，列出关键备份路径

## 输出要求
- 明确告诉用户哪些 Skill 已是最新
- 明确告诉用户哪些 Skill 刚刚补做了备份
- 若有错误，必须单独列出，不得含糊带过
- 不得在未实际执行巡检脚本的情况下声称“已巡检”

## 规则
- 只把包含 `SKILL.md` 的目录视为有效 Skill
- 忽略 `_GLOBAL_SKILL_BACKUP_RULE.md` 这类规则文件
- 巡检本质上允许“检查即补备份”，因此 `success` 表示本次发现差异并已完成补备份
