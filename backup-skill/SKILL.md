---
name: backup-skill
description: |
  把指定 Skill 完整备份到桌面的 SKILL 珍藏库。
  自动识别当前电脑用户名，动态拼接路径，支持多台 Mac（桌面通过 iCloud 同步）。
  自动按版本子目录保存，并生成 BACKUP_MANIFEST.json。
---

## 触发指令
- `备份这个skill`
- `备份这个 Skill`
- `备份skill`
- `备份 Skill`
- `把这个skill备份一下`
- `把这个 Skill 备份一下`
- `备份到skill珍藏库`
- `备份到Skill珍藏库`
- `把刚更新的skill备份`
- `把刚更新的 Skill 备份`

## 适用场景
- 用户要求把某个 Skill 备份到珍藏库
- 某个 Skill 刚创建或刚更新完成，需要立即归档
- 用户要求检查并执行某个 Skill 的版本化备份

## 输入解析
- 优先使用用户明确指出的 Skill 名称或路径
- 如果用户说“这个 Skill / 刚更新的 Skill”，默认指当前正在处理的那个 Skill 目录
- 若存在歧义，先澄清再执行

## 执行步骤
1. **动态解析珍藏库路径**（每次执行前必须先跑这一步，不得硬编码用户名）：
```bash
VAULT="/Users/$(whoami)/Desktop/软件编程/龙虾相关/SKILL珍藏库"
```
   - `$(whoami)` 自动取当前电脑的登录用户名（如 `alandewin`、`alankongx` 等）
   - 桌面通过 iCloud 同步，所以 `Desktop/软件编程/龙虾相关/SKILL珍藏库` 路径在所有 Mac 上一致
   - 若 `$VAULT` 目录不存在，报错提示用户检查 iCloud 桌面同步状态，**不得自行创建**

2. 确认目标 Skill 目录存在，且包含 `SKILL.md`

3. 执行备份（shell 脚本，无需外部工具）：
```bash
SKILL_SRC="$HOME/.easyclaw/skills/<skill_name>"
VERSION="v$(date +%Y-%m-%d-%H%M%S)"
DEST="$VAULT/<skill_name>/$VERSION"
mkdir -p "$DEST"
cp -R "$SKILL_SRC/"* "$DEST/"
```

4. 生成 `BACKUP_MANIFEST.json`（写入 `$DEST`）：
```json
{
  "skill_name": "<skill_name>",
  "version": "<VERSION>",
  "backup_time": "<UTC时间>",
  "source_path": "<SKILL_SRC>",
  "backup_path": "<DEST>",
  "file_count": <文件数>,
  "skill_version": "<SKILL.md 中的 version 字段>",
  "notes": "<本次更新摘要>"
}
```

5. 向用户汇报备份结果（状态 / 备份路径 / 文件数）

## 结果要求
- 必须明确返回是否成功
- 必须返回备份路径
- 若因内容未变化而跳过，也要明确说明 `skipped_same_as_latest`
- 不得在未执行备份脚本的情况下声称“已备份”

## 规则
- 备份的是整个 Skill 目录，不只是 `SKILL.md`
- 珍藏库路径**必须**用 `$(whoami)` 动态获取用户名，**禁止**硬编码 `alankongx` 或 `alandewin` 等具体用户名
- 备份目录结构：`/Users/$(whoami)/Desktop/软件编程/龙虾相关/SKILL珍藏库/<skill_name>/vYYYY-MM-DD-HHMMSS/`
- 若珍藏库根目录不存在，**不得自行创建**，应提示用户检查 iCloud 桌面同步
- 每次生成或更新 Skill 后，原则上都应立即执行本 Skill 或底层备份脚本
