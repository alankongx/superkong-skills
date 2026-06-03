---
name: memory-manager
description: >
  本地记忆中枢 Memory Manager 3.0。跨引擎搜索记忆（qmd + workspace + lossless + openviking + ingest），
  写入每日笔记，审核并晋升长期记忆，扩展文档上下文，执行记忆健康审计。
  触发词：搜索记忆、查找记忆、写入记忆、记忆审计、记忆晋升、memory search、memory write、memory audit、
  enqueue review、promote memory、harvest promotions、expand 文档。
---

# Memory Manager 3.0

本地优先的 Agent 记忆编排层。不替换现有记忆引擎，而是**统一路由**它们。

## 环境

- **根目录**: `~/.easyclaw/workspace/tools/memory-manager-3/`
- **配置文件**: `memory_manager.json`（profile: `macbook`，本机，无 SSH）
- **Python**: 3.11，零外部依赖，纯标准库
- **调用方式**:

```bash
MM3="$HOME/.easyclaw/workspace/tools/memory-manager-3"
MM3_CONFIG="$MM3/memory_manager.json" PYTHONPATH="$MM3/src" \
  python3 -m memory_manager_3.cli --config "$MM3/memory_manager.json" <command>
# 或直接用 shell 脚本（等价）：
$MM3/run-mm3.sh <command>
```

## 适配器架构

| 适配器 | 用途 | 状态 |
|---|---|---|
| `qmd` | 跨 Markdown 文件全文 + BM25 检索（直接读 SQLite，不依赖 qmd CLI） | ✅ 可用 |
| `workspace` | 读写 `~/.easyclaw/workspace/` 核心文件（MEMORY.md / USER.md 等） | ✅ 可用 |
| `lossless` | 会话上下文扩展（`~/.easyclaw/lcm.db`） | ✅ 可用 |
| `openviking` | 长文深度检索（需本地 Ollama，端口 1933） | ⚠️ 按需启动 |
| `ingest` | 多来源摄入（Codex / OpenClaw / Alma / 书籍 / 文章等） | ✅ 可用 |

## 常用命令

### 搜索与上下文

```bash
# 跨引擎混合搜索（推荐）
./run-mm3.sh search "OpenClaw 记忆系统"

# 组装上下文包（搜索 + 拼装，用于注入 Agent 上下文）
./run-mm3.sh context "记忆路由设计"

# 展开单个文档全文（按 backend + 文件路径）
./run-mm3.sh expand --backend qmd \
  --source "obsidian/xxx.md" \
  --collection obsidian \
  --char-limit 4000
```

### 写入记忆

```bash
# 写入每日记忆层（默认 daily）
./run-mm3.sh write-note "今天确认了 memory-manager 3.0 部署完成" --layer daily
```

### 长期记忆审核流

```bash
# 从每日层收割候选晋升条目
./run-mm3.sh harvest-promotions

# 加入审核队列
./run-mm3.sh enqueue-review "OpenClaw 的记忆应以文件为真相源"

# 查看审核队列
./run-mm3.sh list-review

# 审核通过 → 晋升到 MEMORY.md
./run-mm3.sh approve-review <id>
# 或拒绝
./run-mm3.sh reject-review <id>
# 或直接晋升（跳过审核）
./run-mm3.sh promote "OpenClaw 的记忆应以文件为真相源"

# 撤销晋升
./run-mm3.sh remove-promoted "..."
```

### 管理与审计

```bash
# 记忆健康审计
./run-mm3.sh audit

# 查看 ingest 挂载点列表
./run-mm3.sh list-ingest

# 检查兼容性（模型/依赖状态）
./run-mm3.sh compatibility
```

## MCP 服务（给 Cursor / Codex 等 Agent 使用）

```bash
# 启动 stdio MCP server
./run-mm3-mcp.sh
```

暴露的 MCP 工具：`memory_search` / `memory_context` / `memory_expand` /
`memory_write_note` / `memory_audit` / `memory_harvest_promotions` /
`memory_promote` / `memory_enqueue_review` / `memory_list_review` /
`memory_approve_review` / `memory_reject_review` / `memory_remove_review` /
`memory_remove_promoted`

MCP 接入配置：
```json
{
  "mcpServers": {
    "memory-manager-3": {
      "command": "/Users/alankongx/.easyclaw/workspace/tools/memory-manager-3/run-mm3-mcp.sh",
      "args": []
    }
  }
}
```

## 搜索路由逻辑

`MemoryRouter` 根据关键词自动决定调用哪些后端（按权重排序）：

| 触发词 | 优先后端 |
|---|---|
| 记忆 / memory / 长期 / 偏好 / daily | `workspace` |
| 论文 / 报告 / 长文 / research / paper | `openviking` |
| codex / alma / openclaw / article / book / summary | `ingest` |
| 刚才 / 上次 / 会话 / session | `lossless` |
| 其他（兜底） | `qmd` → `ingest` → `workspace` |

## 注意事项

- `qmd` 适配器直接操作 `~/.cache/qmd/index.sqlite`，**不需要 qmd CLI 可运行**（规避了 Node 版本冲突问题）
- 如需重建 qmd 索引（embed），仍需修复 qmd CLI：`npm rebuild` in `/opt/homebrew/lib/node_modules/@tobilu/qmd/`
- 本 Skill 已取代旧版 `qmd` skill（2.0），旧 skill 可安全删除
