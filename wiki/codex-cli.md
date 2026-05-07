# OpenAI Codex CLI

> 研究日期：2026-05-07  
> 状态：实战测试，基于凯戈本机实测（v0.128.0，gpt-5.5 medium，Plus 账号）  
> 研究员：凯戈 + Claude  
> 注意：官方文档以 openai.com 为准；本页结论来自终端实测，非二手转述

---

## 一、基本信息

| 项 | 内容 |
|----|------|
| 工具名 | OpenAI Codex CLI |
| 当前版本 | v0.128.0（2026-05-07 实测）|
| 默认模型 | gpt-5.5 medium |
| 切换模型 | `/model` 命令 |
| 启动命令 | `codex` |
| App 模式 | `codex app` 或访问 chatgpt.com/codex |
| 订阅要求 | Plus 及以上（免费账号 MCP 不可用）|
| 官方文档 | openai.com/codex（需登录）|

---

## 二、MCP 集成（`/mcp` 命令查看）

Codex CLI Plus 账号默认挂载 `codex_apps`，包含两大服务：

### GitHub MCP（~70 个工具）

覆盖范围极广：

- **读操作**：fetch_commit / fetch_issue / fetch_pr / search_commits / search_issues / get_repo / compare_commits / fetch_workflow_job_logs 等
- **写操作**：create_issue / create_pull_request / create_branch / create_file / update_file / delete_file / merge_pull_request / add_review_to_pr 等
- **搜索**：search / search_repositories / search_issues / search_prs / search_branches 等

**已知盲区（2026-05-07 实测）**：

| 操作 | 支持情况 | 说明 |
|------|----------|------|
| 列出仓库最新 commits | ❌ 无直接工具 | 只有 `search_commits`（需搜索文本）|
| 按时间无条件枚举 commits | ❌ | `search_commits` 必须有查询词，纯 qualifier 返回 422 |
| Issue / PR 写入 | ✅ | 一次成功，无问题 |

**Codex 自恢复行为**：遇到 422 时主动换策略（先搜索定位仓库全名再重试），4 次调用后自我诊断"用错了工具语义"——Agent 容错能力强。

### Canva MCP（~30 个工具）

覆盖设计全流程：

- **读操作**：list_folder_items / get_design / get_design_content / get_design_pages / get_design_thumbnail / list_brand_kits / get_assets / search / search_designs 等
- **写操作**：create_design_from_candidate / generate_design / perform_editing_operations / upload_asset_from_url / resize_design / create_folder 等
- **Auth**：Bearer token，绑定 Canva 账号（免费版可用）

**已知行为（2026-05-07 实测）**：

- `list_folder_items` 读取真实设计列表 ✅，数据准确
- `create_design_from_candidate` 创建设计 ✅，但标题不完全保留指令原文（由 Canva API 决定）

---

## 三、代码 Agent 能力（实测评估）

测试任务：在 `vex-iq-kb/tools/` 新建 `wiki_stats.py`（扫描 wiki 字数 + wikilink 统计）

| 行为维度 | 表现 | 评分 |
|----------|------|------|
| 遵守项目规则（AGENTS.md / WIP.md）| 开工前主动读取 | ✅ |
| 设计先行 | 先出方案等批准再动代码 | ✅ |
| 代码质量 | 标准库，逻辑清晰，< 60 行 | ✅ |
| 自验证 | 自跑 `py_compile` + 实际执行 | ✅ |
| 文档维护 | 主动追加更新 AGENTS.md / WIP.md / dev_log.md | ✅ |
| Git 规范 | commit 前汇报文件列表，不自行 push，等确认 | ✅ |

**结论**：代码 Agent 规范性高，可信任执行中等难度任务。遵守外部规则文件（AGENTS.md）的能力与 Claude Code 相当。

---

## 四、与 Claude Code 对比

| 维度 | Codex CLI | Claude Code |
|------|-----------|-------------|
| 代码 Agent 规范性 | ✅ 高 | ✅ 高 |
| GitHub 操作 | ⚠️ MCP，有盲区（无 list_commits）| ✅ 本地 git + gh CLI，无限制 |
| Canva 集成 | ✅ 官方 MCP | ❌ 无 |
| PDF 能力 | ⚠️ 无专用 MCP 工具（需 Skills）| ✅ fitz / pdf skill |
| 本地文件操作 | ✅ | ✅ |
| Shell 执行 | ✅ | ✅ |
| 自定义规则文件 | AGENTS.md | CLAUDE.md |
| 订阅要求 | Plus（MCP 需官方账号）| Pro / Max |

---

## 五、踩坑记录

| 坑 | 现象 | 解法 |
|----|------|------|
| 第三方 token 登录 | MCP 全部报错 | 改用官方账号登录 |
| `search_commits` 误用 | 只用 qualifier 返回 422 | 必须加搜索文本词 |
| 仓库全名不确定 | `kaigeliao/vex-iq-kb` 422 | 先用 search 定位 `kaigeliao2018/vex-iq-kb` |
| Canva 标题不保留 | 指令标题变为模板名 | Canva API 行为，非 Codex 问题 |

---

## 六、适用场景建议

| 场景 | 推荐工具 | 说明 |
|------|----------|------|
| 代码 Agent 任务 | Codex CLI ≈ Claude Code | 两者规范性相当，按订阅选 |
| GitHub 全功能操作 | Claude Code | 本地 git / gh 无盲区 |
| Canva 设计自动化 | Codex CLI | 独有能力 |
| PDF 解析 | Claude Code | fitz 主选，pdf skill 辅助 |
| 需要 gpt-5.5 模型 | Codex CLI | Claude Code 只走 Claude 系列 |

---

## 七、一个重要反例：GPT-5.5 网页版不了解自己的 CLI MCP

> 研究日期：2026-05-07  
> 来源：凯戈直接向 ChatGPT 网页版提问「你的 MCP 是什么能力？」

**GPT-5.5 网页版的回答把 MCP 解读为：**

> M = Multimodal / C = Competent / P = Personalized

这是**完全编造的缩写**，与 Model Context Protocol 无关。

**其他错误**：

| 网页版说法 | 实际情况 |
|-----------|----------|
| `npm install -g @openai/cli` | 实际安装包名不同，CLI 命令为 `codex` |
| `openai chat --role "助教"` | 不存在此命令，正确命令是直接运行 `codex` |
| MCP = Multimodal/Competent/Personalized | MCP = Model Context Protocol，是 GitHub + Canva 工具集成层 |
| CLI 可用 `gpt chat --input` | 不存在此用法 |

**根因**：GPT-5.5 网页版的训练数据中对 Codex CLI 的 MCP 实现细节认知不足，遇到"MCP"缩写时用了错误的语义填充。这是跨产品知识割裂的典型案例——同一家公司的两个产品（网页版 vs CLI）之间，网页版对 CLI 能力的自述不可信。

**教训**：了解 Codex CLI 真实能力，必须靠实测（`/mcp` 命令），不能靠问网页版 ChatGPT。

---

## See Also

- [[gpt-5.5]] — gpt-5.5 模型本体研究
- [[pdf-skill-vs-fitz]] — PDF 解析能力横向对比
- [[skills-ecosystem]] — Claude Skills 生态地图
