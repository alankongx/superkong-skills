# superkong-skills

Alan Kong 开源的 AI Agent Skills 合集。

我在新加坡做跨境贸易 B2B，帮欧美客户从越南和中国采购定制零部件。这些 skill 是我日常真正在用的——从工程图纸翻译、供应商询价单生成，到记忆管理、磁盘清理。跑通了才开源，没什么花活。

每个 Skill 都是 Agent 可以直接加载的结构化指令集，遵循 [Agent Skills](https://agentskills.io) 开放标准。支持 Claude Code、Codex、OpenClaw 等平台。

---

## Skills 列表

| 名称 | 一句话 | 场景 |
|------|--------|------|
| 📦 [rfq-generator](#-rfq-generator) | 上传工程图纸，自动生成中英双语供应商询价单 | 跨境贸易 |
| 💰 [quotation-generator](#-quotation-generator) | 从 RFQ 到客户报价单 + 内部定价表，一步生成 | 跨境贸易 |
| 📐 [drawing-translator](#-drawing-translator) | 自动提取 PDF 工程图纸中的外文标注，翻译成中文并批注回图 | 跨境贸易 |
| 🧾 [baoxiao](#-baoxiao) | 出差报销全流程：收发票 → 核对 → 生成明细表 Excel | 财务行政 |
| 🧹 [neat-freak](#-neat-freak) | 干完活跑一下，自动把改动和项目文档/记忆全部对齐 | 工作流 |
| 🧠 [memory-manager](#-memory-manager) | 本地记忆中枢，跨会话搜索、写入、晋升长期记忆 | 工作流 |
| 🔍 [memory-audit-guardian](#-memory-audit-guardian) | 周度记忆治理审计，清理过期条目、降低 token 开销 | 工作流 |
| 🗂️ [skill-router](#-skill-router) | 说一句话自动路由到对应 Skill，统一调度入口 | 工作流 |
| 🛡️ [skill-vetter](#-skill-vetter) | 安装 skill 前先做安全审查，识别恶意权限和注入风险 | 安全 |
| 🔨 [skill-creator](#-skill-creator) | 从零创建 skill、优化描述、跑 eval 测试性能 | 开发 |
| 💾 [backup-skill](#-backup-skill) | 把指定 Skill 完整备份到本地 SKILL 珍藏库 | 工具 |
| 🔎 [audit-skill-backups](#-audit-skill-backups) | 巡检 Skill 备份状态，找出哪些没备份并自动补做 | 工具 |
| 📄 [docx](#-docx) | 创建/编辑 Word 文档，支持批注、修订、格式保留 | 文档 |
| 🖨️ [pdf-ocr-extraction](#-pdf-ocr-extraction) | 扫描版 PDF 文字提取，离线运行，输出为 Word 文件 | 文档 |
| 🎨 [handdrawn-prompt-writer](#-handdrawn-prompt-writer) | 把任意主题转化为高质量手绘风 AI 出图提示词 | 设计 |
| 🎬 [libtv-skill](#-libtv-skill) | 通过 liblib.tv 生成和编辑 AI 图片/视频 | 设计 |
| 🔭 [github-operations](#-github-operations) | GitHub 仓库管理、Issue/PR、文件读写，本地 token 驱动 | 开发 |
| 🔎 [tavily-search](#-tavily-search) | Tavily API 网页搜索，Brave 不可用时的备用方案 | 搜索 |
| 📰 [wechat-article-search](#-wechat-article-search) | 搜狗接口搜索微信公众号文章，返回标题/摘要/链接 | 搜索 |
| 🌐 [web-content-fetcher](#-web-content-fetcher) | 多策略网页正文提取，输出干净 Markdown | 搜索 |
| 🎓 [adaptive-socratic-questioning](#-adaptive-socratic-questioning) | 自适应苏格拉底追问，引导学生深度思考、挖掘误解 | 教育 |

---

## 安装

在支持 SKILL.md 的 Agent（Claude Code、Codex、OpenClaw 等）里直接说：

```
帮我安装这个 skill：https://github.com/alankongx/superkong-skills/tree/main/<skill-name>
```

把 `<skill-name>` 换成你要装的那个，比如 `rfq-generator`、`neat-freak`、`memory-manager`。

---

## 详细介绍

### 📦 rfq-generator

**自动识别工程 PDF / 图片图纸，提取零件参数，结合客户询价信息，生成中英双语供应商询价单（.xlsx）。**

自动补充客户原始信息中模板未覆盖的字段，默认执行父子项关系标准化、中文工程术语翻译、版式规范化与最终校对。适合精密零部件跨境采购场景。

→ [SKILL.md](rfq-generator/SKILL.md)

---

### 💰 quotation-generator

**从 RFQ + 供应商报价，一键生成客户报价单和内部定价审核工作簿。**

遵循 Dewin 工作流默认值，自动计算利润区间、汇率换算、交期填写。

→ [SKILL.md](quotation-generator/SKILL.md)

---

### 📐 drawing-translator

**上传 PDF 工程图纸，自动提取所有非中文标注文字，翻译为中文，以批注形式写回 PDF。**

输出带中文标注的新 PDF，按真实样本命名（如 `000029-RBS-MainPlate_3_CN.pdf`）。适合接收海外客户图纸时的快速阅图场景。

→ [SKILL.md](drawing-translator/SKILL.md)

---

### 🧾 baoxiao

**出差报销全流程助理：收集发票凭证 → 核对金额 → 填写报销明细表 → 输出 Excel 文件。**

→ [SKILL.md](baoxiao/SKILL.md)

---

### 🧹 neat-freak

**每次任务做完，跑一下 `/neat`，自动把这次会话改的东西和项目文档、AGENTS.md、Agent 记忆全部对齐，输出变更摘要。**

解决的核心问题：代码迭代了七八轮，文档还是最初那版；记忆里写着用 SQLite，其实早换 PostgreSQL 了。

触发方式：`/neat` · `整理一下` · `同步一下` · `sync up`

→ [SKILL.md](neat-freak/SKILL.md)

---

### 🧠 memory-manager

**本地记忆中枢 Memory Manager 3.0。跨引擎搜索记忆、写入每日笔记、审核并晋升长期记忆、扩展文档上下文。**

支持 qmd / workspace / lossless / openviking / ingest 五个适配器，无需 qmd CLI 运行，直接读本机 SQLite。

→ [SKILL.md](memory-manager/SKILL.md)

---

### 🔍 memory-audit-guardian

**周度记忆治理审计。清理过期条目、压缩 token 开销、校验 MEMORY 和 TOOLS 的职责边界。**

→ [SKILL.md](memory-audit-guardian/SKILL.md)

---

### 🗂️ skill-router

**本地 Skill 自动路由与调度器。扫描 `skills/*/SKILL.md`，建立索引，按用户指令或意图匹配最合适的 Skill，支持 dispatch/auto 执行。**

→ [SKILL.md](skill-router/SKILL.md)

---

### 🛡️ skill-vetter

**安全优先的 Skill 审查工具。安装任何来自 ClawdHub、GitHub 或第三方的 skill 前，先跑一遍，检查红旗项、权限范围和可疑注入模式。**

→ [SKILL.md](skill-vetter/SKILL.md)

---

### 🔨 skill-creator

**从零创建新 skill、修改和优化已有 skill、跑 eval 测试性能，支持描述词优化以提升路由准确率。**

→ [SKILL.md](skill-creator/SKILL.md)

---

### 💾 backup-skill

**把指定 Skill 完整备份到桌面的 SKILL 珍藏库（自动按版本子目录保存，生成 BACKUP_MANIFEST.json）。支持多台 Mac iCloud 同步。**

→ [SKILL.md](backup-skill/SKILL.md)

---

### 🔎 audit-skill-backups

**巡检工作区中所有 Skill 与珍藏库最新备份是否一致，找出哪些没有备份，并在需要时自动补做。**

→ [SKILL.md](audit-skill-backups/SKILL.md)

---

### 📄 docx

**Word 文档全能处理：创建、编辑、批注、修订跟踪、格式保留、文本提取。**

→ [SKILL.md](docx/SKILL.md)

---

### 🖨️ pdf-ocr-extraction

**扫描版 PDF 文字提取，使用 EasyOCR，完全离线，无需 API Key，输出 Word（.docx）文件。**

→ [SKILL.md](pdf-ocr-extraction-v1.0.0/SKILL.md)

---

### 🎨 handdrawn-prompt-writer

**把任意主题做成手绘风、涂鸦风、信息图风 AI 出图提示词。输出高质量中文 prompt，附版式、配色、构图、负面约束与风格变体。**

→ [SKILL.md](handdrawn-prompt-writer/SKILL.md)

---

### 🎬 libtv-skill

**通过 liblib.tv 生成和编辑 AI 图片/视频。覆盖文生图、文生视频、图生视频、风格迁移、产品广告片、短剧生成等场景。**

→ [SKILL.md](libtv-skill/SKILL.md)

---

### 🔭 github-operations

**GitHub 仓库管理全能工具：创建/删除仓库、文件读写、Issue/PR 管理、分支操作、代码搜索。本地 Token 驱动，无需 gh CLI。**

→ [SKILL.md](github-operations/SKILL.md)

---

### 🔎 tavily-search

**通过 Tavily API 进行网页搜索，Brave search 不可用时的备用方案。返回标题、URL、摘要，可选包含摘要答案。**

→ [SKILL.md](tavily-search/SKILL.md)

---

### 📰 wechat-article-search

**通过搜狗微信搜索接口，按关键词检索微信公众号文章，返回标题、摘要、发布时间、来源和链接。**

→ [SKILL.md](wechat-article-search/SKILL.md)

---

### 🌐 web-content-fetcher

**网页正文内容提取。Jina Reader / Scrapling+html2text / web_fetch 三级降级策略，输出干净 Markdown，保留标题、链接、图片 URL。支持微信公众号文章。**

→ [SKILL.md](web-content-fetcher/SKILL.md)

---

### 🎓 adaptive-socratic-questioning

**自适应苏格拉底式追问技能。通过逐层追问引导学生深度推理，挖掘认知误区，适用于教学辅导和批判性思维培养。**

→ [SKILL.md](adaptive-socratic-questioning-1.1.0/SKILL.md)

---

## 关于我

我是 Alan Kong，新加坡 [DEWIN Technology](https://dewintech.com) 创始人。我们帮助欧美客户从越南和中国双源采购定制零部件（CNC 加工、压铸、冲压、注塑等），消除供应链单点风险。

这些 skill 是我在 EasyClaw / OpenClaw 里日常跑通的工具，觉得有用就开源了。如果对你有帮助，给个 ⭐ 就行。有问题欢迎开 Issue。