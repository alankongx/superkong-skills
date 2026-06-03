---
name: github-operations
description: GitHub 操作技能，支持仓库管理、Issue/PR 处理、文件操作、代码搜索、分支管理等。触发条件包括 "GitHub", "仓库", "Issue", "PR", "Pull Request", "代码搜索", "创建仓库", "合并" 等。
---

# GitHub 操作专家

## 角色定位

帮助用户在 GitHub 上执行各种操作，包括仓库管理、Issue 和 PR 处理、文件读写、代码搜索和分支管理等。

**支持读取和写入操作。**

## ⚠️ AI 使用规范（重要）

**本技能使用本地 API Key 模式，Token 保存在 `~/.easyclaw/linkapp/github_api_key`。**

首次使用需要：
1. 前往 GitHub Settings → Developer settings → Personal access tokens
2. 创建 Token（权限：`repo`, `workflow`, `read:user`）
   - ⚠️ 如需使用 `delete_repo` 删除仓库，还需额外勾选 `delete_repo` 权限
3. 将 Token 保存到 `~/.easyclaw/linkapp/github_api_key` 文件中

---

## 调用方式

```bash
python <skill_path>/run.py <method> [参数...]
```

**Methods:**
- `check_gate` - 检查 GitHub 连接状态
- `get_me` - 获取当前用户信息
- `get_user` - 获取指定用户信息
- `create_repo` - 创建仓库
- `get_repo` - 获取仓库信息
- `delete_repo` - 删除仓库
- `list_repos` - 列出仓库
- `get_file` - 获取文件内容
- `write_file` - 创建/更新文件
- `list_dir` - 列出目录内容
- `create_issue` - 创建 Issue
- `get_issue` - 获取 Issue
- `update_issue` - 更新 Issue
- `list_issues` - 列出 Issues
- `create_pr` - 创建 Pull Request
- `get_pr` - 获取 PR
- `list_prs` - 列出 PRs
- `merge_pr` - 合并 PR
- `create_comment` - 创建 Issue/PR 评论
- `search_code` - 搜索代码
- `search_repos` - 搜索仓库
- `list_branches` - 列出分支
- `create_branch` - 创建分支

**参数格式:**
- `--key value` - 参数名和值用空格分隔
- `--json` - 输出 JSON 格式（默认输出格式化文本）
- `--private` / `--draft` - 布尔标志（不需要值）

---

## 快速示例

```bash
# 检查连接状态
python run.py check_gate

# 获取用户信息
python run.py get_me
python run.py get_user --username octocat

# 仓库管理
python run.py create_repo --name my-project --description "我的项目"
python run.py create_repo --name private-repo --private
python run.py get_repo --owner octocat --repo Hello-World
python run.py list_repos --username octocat --sort stars --order desc
python run.py delete_repo --owner me --repo old-project

# 文件操作
python run.py get_file --owner octocat --repo Hello-World --path README.md
python run.py write_file --owner me --repo my-repo --path test.txt --content "Hello World" --message "Add test file"
python run.py write_file --owner me --repo my-repo --path test.txt --content "Updated" --message "Update file" --sha abc123
python run.py list_dir --owner octocat --repo Hello-World --path src
python run.py list_dir --owner octocat --repo Hello-World --path src --branch develop

# Issue 管理
python run.py create_issue --owner me --repo my-repo --title "Bug found" --body "Description of the bug"
python run.py create_issue --owner me --repo my-repo --title "Feature" --labels "enhancement,help wanted" --assignees "user1,user2"
python run.py get_issue --owner me --repo my-repo --number 1
python run.py list_issues --owner me --repo my-repo --state open
python run.py update_issue --owner me --repo my-repo --number 1 --state closed --labels "bug,fixed"

# PR 管理
# ⚠️ create_pr 前提：head 和 base 必须是不同的分支，且 head 分支已存在
# 如果没有目标分支，先用 create_branch 创建
python run.py create_branch --owner me --repo my-repo --branch feature/new-feature
python run.py create_pr --owner me --repo my-repo --title "New feature" --head feature/new-feature --base main
python run.py create_pr --owner me --repo my-repo --title "WIP" --head dev --draft
python run.py get_pr --owner me --repo my-repo --number 1
python run.py list_prs --owner me --repo my-repo --state open
python run.py merge_pr --owner me --repo my-repo --number 1

# 评论
python run.py create_comment --owner me --repo my-repo --number 1 --body "Thanks for the report!"

# 搜索
python run.py search_code --query "import react language:typescript"
python run.py search_repos --query "machine learning framework" --sort stars --order desc

# 分支
python run.py list_branches --owner octocat --repo Hello-World

# JSON 输出
python run.py get_me --json
```

---

## 方法详情

### 连接检查

#### `check_gate` - 检查 GitHub 连接状态

无需参数，返回连接状态和当前用户信息。

```bash
python run.py check_gate
```

### 用户操作

#### `get_me` - 获取当前用户信息

| 参数 | 说明 | 必需 |
|------|------|------|
| 无 | - | - |

#### `get_user` - 获取指定用户信息

| 参数 | 说明 | 必需 |
|------|------|------|
| `--username` | GitHub 用户名 | 是 |

### 仓库操作

#### `create_repo` - 创建仓库

| 参数 | 说明 | 必需 |
|------|------|------|
| `--name` | 仓库名称 | 是 |
| `--description` | 仓库描述 | 否 |
| `--private` | 创建私有仓库（标志） | 否 |

#### `get_repo` - 获取仓库信息

| 参数 | 说明 | 必需 |
|------|------|------|
| `--owner` | 仓库所有者 | 是 |
| `--repo` | 仓库名 | 是 |

#### `delete_repo` - 删除仓库

| 参数 | 说明 | 必需 |
|------|------|------|
| `--owner` | 仓库所有者 | 是 |
| `--repo` | 仓库名 | 是 |

#### `list_repos` - 列出仓库

| 参数 | 说明 | 必需 |
|------|------|------|
| `--username` | 用户名（默认当前用户） | 否 |
| `--sort` | 排序字段（stars/updated/created） | 否 |
| `--order` | 排序方向（asc/desc） | 否 |
| `--per_page` | 每页数量 | 否（默认30） |
| `--page` | 页码 | 否（默认1） |

### 文件操作

#### `get_file` - 获取文件内容

| 参数 | 说明 | 必需 |
|------|------|------|
| `--owner` | 仓库所有者 | 是 |
| `--repo` | 仓库名 | 是 |
| `--path` | 文件路径 | 是 |
| `--branch` | 分支名 | 否 |

#### `write_file` - 创建/更新文件

| 参数 | 说明 | 必需 |
|------|------|------|
| `--owner` | 仓库所有者 | 是 |
| `--repo` | 仓库名 | 是 |
| `--path` | 文件路径 | 是 |
| `--content` | 文件内容 | 是 |
| `--message` | Commit 消息 | 是 |
| `--branch` | 分支名 | 否 |
| `--sha` | 文件 SHA（更新时需要） | 否 |

#### `list_dir` - 列出目录内容

| 参数 | 说明 | 必需 |
|------|------|------|
| `--owner` | 仓库所有者 | 是 |
| `--repo` | 仓库名 | 是 |
| `--path` | 目录路径 | 否（默认根目录） |
| `--branch` | 分支名 | 否 |

### Issue 操作

#### `create_issue` - 创建 Issue

| 参数 | 说明 | 必需 |
|------|------|------|
| `--owner` | 仓库所有者 | 是 |
| `--repo` | 仓库名 | 是 |
| `--title` | Issue 标题 | 是 |
| `--body` | Issue 正文 | 否 |
| `--labels` | 标签（逗号分隔） | 否 |
| `--assignees` | 指派人（逗号分隔） | 否 |

#### `get_issue` - 获取 Issue

| 参数 | 说明 | 必需 |
|------|------|------|
| `--owner` | 仓库所有者 | 是 |
| `--repo` | 仓库名 | 是 |
| `--number` | Issue 编号 | 是 |

#### `update_issue` - 更新 Issue

| 参数 | 说明 | 必需 |
|------|------|------|
| `--owner` | 仓库所有者 | 是 |
| `--repo` | 仓库名 | 是 |
| `--number` | Issue 编号 | 是 |
| `--title` | 新标题 | 否 |
| `--body` | 新正文 | 否 |
| `--state` | 状态（open/closed） | 否 |
| `--labels` | 标签（逗号分隔） | 否 |
| `--assignees` | 指派人（逗号分隔） | 否 |

#### `list_issues` - 列出 Issues

| 参数 | 说明 | 必需 |
|------|------|------|
| `--owner` | 仓库所有者 | 是 |
| `--repo` | 仓库名 | 是 |
| `--state` | 状态过滤（open/closed） | 否（默认open） |
| `--labels` | 标签过滤 | 否 |
| `--per_page` | 每页数量 | 否（默认30） |
| `--page` | 页码 | 否（默认1） |

### PR 操作

#### `create_pr` - 创建 Pull Request

| 参数 | 说明 | 必需 |
|------|------|------|
| `--owner` | 仓库所有者 | 是 |
| `--repo` | 仓库名 | 是 |
| `--title` | PR 标题 | 是 |
| `--head` | 源分支 | 是 |
| `--base` | 目标分支 | 否（默认main） |
| `--body` | PR 正文 | 否 |
| `--draft` | 草稿 PR（标志） | 否 |

#### `get_pr` - 获取 PR

| 参数 | 说明 | 必需 |
|------|------|------|
| `--owner` | 仓库所有者 | 是 |
| `--repo` | 仓库名 | 是 |
| `--number` | PR 编号 | 是 |

#### `list_prs` - 列出 PRs

| 参数 | 说明 | 必需 |
|------|------|------|
| `--owner` | 仓库所有者 | 是 |
| `--repo` | 仓库名 | 是 |
| `--state` | 状态过滤（open/closed） | 否（默认open） |
| `--per_page` | 每页数量 | 否（默认30） |
| `--page` | 页码 | 否（默认1） |

#### `merge_pr` - 合并 PR

| 参数 | 说明 | 必需 |
|------|------|------|
| `--owner` | 仓库所有者 | 是 |
| `--repo` | 仓库名 | 是 |
| `--number` | PR 编号 | 是 |

### 评论操作

#### `create_comment` - 创建 Issue/PR 评论

| 参数 | 说明 | 必需 |
|------|------|------|
| `--owner` | 仓库所有者 | 是 |
| `--repo` | 仓库名 | 是 |
| `--number` | Issue/PR 编号 | 是 |
| `--body` | 评论内容 | 是 |

### 搜索操作

#### `search_code` - 搜索代码

| 参数 | 说明 | 必需 |
|------|------|------|
| `--query` | 搜索关键词 | 是 |
| `--per_page` | 每页数量 | 否（默认30） |
| `--page` | 页码 | 否（默认1） |

**搜索语法示例：**
- `import react language:typescript`
- `filename:config.json keyword`
- `repo:owner/name function_name`

#### `search_repos` - 搜索仓库

| 参数 | 说明 | 必需 |
|------|------|------|
| `--query` | 搜索关键词 | 是 |
| `--sort` | 排序字段（stars/forks/updated） | 否（默认stars） |
| `--order` | 排序方向（asc/desc） | 否（默认desc） |
| `--per_page` | 每页数量 | 否（默认30） |
| `--page` | 页码 | 否（默认1） |

### 分支操作

#### `list_branches` - 列出分支

| 参数 | 说明 | 必需 |
|------|------|------|
| `--owner` | 仓库所有者 | 是 |
| `--repo` | 仓库名 | 是 |
| `--per_page` | 每页数量 | 否（默认30） |
| `--page` | 页码 | 否（默认1） |

---

## 错误处理

| 错误 | 原因 | 解决 |
|------|------|------|
| 尚未连接 GitHub | Token 未配置 | 创建 Token 保存到 ~/.easyclaw/linkapp/github_api_key |
| Token 无效或已过期 | Token 被撤销或过期 | 重新创建 Token |
| Repository not found | 仓库不存在或无权限 | 检查仓库名和权限 |
| Not Found | 资源不存在 | 检查编号和路径 |
| 权限不足 | Token 权限不够 | 检查 Token 的 scope 设置 |

---

## AI Agent 使用指南

### 核心原则

**一次调用，直接展示。** 直接调用对应方法，返回结果原样展示给用户。

### 使用流程

1. **直接调用操作方法**（如 `create_issue`, `get_file`, `create_pr` 等）
2. **如果返回需要连接 GitHub**（`data.need_connect === true`）→ 可以做必要的说明，但必须将以下按钮**放在整个回复的最末尾**，作为最后一行输出，按钮后不得再追加任何文字：
   ```
   <button data-connect-action="apikey" data-platform-id="github">填写 授权信息</button>
   ```
   > 如果 `error` 包含 `delete_repo`，说明是 Token 缺少 `delete_repo` 权限，请提示用户重新生成 Token 并勾选该权限，而不是显示连接按钮。
3. **获取结果后** → 将 formatted 输出或 JSON 数据展示给用户

---

## 调用方式

`run.py` 和本 SKILL.md 文件在同一个目录。执行前先获取本 SKILL.md 的绝对路径的目录作为 `<skill_path>`，run.py 的路径就是 `<skill_path>/run.py`。

⚠️ 禁止使用推断或猜测的路径，必须以读取本文件时的实际绝对路径为准。

## 核心脚本

`run.py` — 跨平台统一入口，使用 `Path(__file__).resolve()` 自动定位脚本路径，无需 `cd` 切换目录，macOS 和 Windows 命令结构完全相同。

**`run.py` 位于本 SKILL.md 的同级目录。** 执行前先确认本文件的实际读取路径，取其所在目录作为 `<skill_path>`。
