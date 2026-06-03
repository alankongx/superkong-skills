---
name: skill-router
description: |
  本地 Skill 自动路由与调度器。用于扫描 `skills/*/SKILL.md`，建立技能索引，按用户指令或意图匹配最可能的 Skill，并支持 dispatch/auto 执行已知入口脚本。
  适用于：
  - 用户希望“说一句话就自动匹配到对应 Skill”
  - 需要对 `翻译标注图纸`、`生成询价单`、`备份 Skill` 等意图做统一路由
  - 需要把 Skill 匹配、调度、日志记录固化成可复用基础设施
---

## 功能
1. 扫描工作区 `skills/*/SKILL.md` 建立索引
2. 从 `SKILL.md` 提取：名称、描述、触发词、关键词
3. 对用户输入进行匹配打分并返回候选 Skill
4. 支持 `dispatch`：返回命中的 Skill、入口、参数建议
5. 支持 `auto`：对部分已知 Skill 自动执行完整或半完整流程

## 当前已支持的自动路由目标
- `drawing-translator`
- `rfq-generator`
- `backup-skill`（当前以 manual dispatch 为主）

## 用法
### 建索引
```bash
python3 skill_router.py build
```

### 匹配一句话
```bash
python3 skill_router.py match "翻译标注图纸"
```

### 调度
```bash
python3 skill_router.py dispatch "帮我生成询价单"
```

### 自动执行
```bash
python3 skill_router.py auto "翻译标注图纸" /path/to/pdf_or_dir
python3 skill_router.py auto "帮我生成询价单" input.json output.xlsx
```

## 说明
- 这是工作区级基础设施，不是单一业务 Skill 的产物
- 建议与工作区中的其他 Skill 一起版本化管理
- 每次重要更新后应备份到 Skill 珍藏库
