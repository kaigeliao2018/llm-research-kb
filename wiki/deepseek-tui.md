---
name: DeepSeek-TUI 全面研究
description: Hmbown/DeepSeek-TUI 项目身份、能力清单、与 PierrunoYT/deepseek-cli 区别、与 Claude Code 设计对比、macOS 装机踩坑实录、第一次跑通指南
type: research
date: 2026-05-08
related: deepseek-v4 / codex-cli / skills
---

# DeepSeek-TUI 全面研究（2026-05-08）

> 研究日期：2026-05-08（V4 发布后两周窗口）
> 研究员：凯戈 + Claude（Opus 4）+ general-purpose agent
> 配套：装机实测同日完成（凯戈 macOS Tahoe 26.4.1）

---

## 一、项目身份（GitHub API 实证）

| 项 | 值 | 来源 |
|---|---|---|
| 仓库 | [Hmbown/DeepSeek-TUI](https://github.com/Hmbown/DeepSeek-TUI) | GitHub |
| 作者 | Hunter Bown (`@Hmbown`)，Twitter `@huntermbown`，**独立开发者** | GitHub profile |
| 创建 | 2026-01-19 | GitHub API |
| 最新版本 | **v0.8.20**（2026-05-08，hotfix） | GitHub releases |
| Stars | **20,095**（4 个月增长） [验证通过] | GitHub API |
| Forks / Issues | 1,539 / 271 open | GitHub API |
| License | MIT | 仓库 LICENSE |
| 主语言 | **Rust** + ratatui TUI | GitHub API + README |
| 官方背书 | ✅ 收录于 `deepseek-ai/awesome-deepseek-agent`，描述："Open-source Rust terminal coding assistant for DeepSeek-V4 — Codex-style architecture, sandboxed tools, MCP client + server, 1M context." | [awesome-deepseek-agent](https://github.com/deepseek-ai/awesome-deepseek-agent) |
| 隶属声明 | "Not affiliated with DeepSeek Inc."（README 显式声明） | README |
| 致谢 | DataWhale 社区"鲸兄弟"接纳；多名中国 contributor | README |

---

## 二、与 PierrunoYT/deepseek-cli 的区别（重要！）

容易混淆的两个项目：

| 维度 | **Hmbown/DeepSeek-TUI** ✅ | PierrunoYT/deepseek-cli ❌ |
|---|---|---|
| 类型 | **Agentic CLI**（对标 Claude Code） | 纯聊天前端 |
| 语言 | Rust | Python |
| 命令 | `deepseek` | `deepseek` |
| 安装 | `brew install deepseek-tui` 或 npm | `pip install deepseek-cli` |
| 工具调用 | 文件读写、shell、git、web、MCP、子 agent | 无 |
| 模式 | Plan / Agent / YOLO | 仅聊天 |
| Skills | 扫 `.claude/skills` 等多目录 | 无 |
| 上下文 | 1M token | 仅 chat history |
| 适合 | 工程任务、研究 | 试聊天 |

**铁律**：研究 V4 真实能力请用 **Hmbown/DeepSeek-TUI**，PierrunoYT 那个是聊天玩具。

---

## 三、装机实录（macOS）+ 三个踩坑（实证）

### 标准装机命令

```bash
brew tap Hmbown/deepseek-tui
brew install deepseek-tui

# 配代理（README 没文档化，走 reqwest 标准 env）
export HTTPS_PROXY=http://127.0.0.1:7897
export HTTP_PROXY=http://127.0.0.1:7897

# API key 从 .zshrc 的 DEEPSEEK_API_KEY env 读取（不必再 auth set）
deepseek doctor   # 全绿才能继续
deepseek          # 启动 TUI
```

### 踩坑 1：`/usr/local/bin/deepseek` 已被旧 pip 包占用

**症状**：`brew install` 完成但显示 `Could not symlink bin/deepseek`，新装的 TUI 不在 PATH。

**根因**：之前装的 `PierrunoYT/deepseek-cli`（pip 包）已经把 `/usr/local/bin/deepseek` 占了。

**修复**：`brew link --overwrite deepseek-tui`

### 踩坑 2：pip uninstall 误删 brew symlink

**症状**：`brew link --overwrite` 成功 → `pip3 uninstall deepseek-cli` 后，`/usr/local/bin/deepseek` 文件**消失**，TUI 不可用。

**根因**：`pip uninstall` 按 RECORD 文件删路径，**它不知道**这个路径已经被 brew 接管了符号链接，无脑删除。

**修复**：
```bash
brew unlink deepseek-tui && brew link deepseek-tui
```
注意：单跑 `brew link --overwrite` 不行（brew 状态记录认为"已 linked"会拒绝），必须先 unlink。

### 踩坑 3：zsh 命令 hash 缓存

**症状**：pip 卸载后即使 brew symlink 在，`deepseek --version` 仍报 `command not found`。

**根因**：zsh hash 表缓存了已删除的旧 pip 路径，不扫 PATH。

**修复**：`hash -r`（清空 hash 表，瞬时生效）

### 踩坑 4：`pip uninstall` 顺序错误

**正确顺序**（吸取经验）：
1. **先** `pip uninstall deepseek-cli`（清干净旧 pip 包）
2. **再** `brew install deepseek-tui` + `brew link`（让 brew 创建全新 symlink）

如果反过来，必踩坑 2。

---

## 四、核心能力（README 一手 + doctor 实证）

1. **三模式**：Plan（只读，输出 plan）/ Agent（默认带审批门）/ YOLO（信任目录全自动）
2. **工具集**：file ops、shell、git、web search/browse、apply-patch、sub-agents、MCP 客户端、`rlm_query`（V4-Flash 子模型批量分析）、LSP 诊断
3. **MCP 双角色**：客户端 + 服务端（`deepseek mcp-server` 起 stdio MCP server）
4. **多 provider**：默认 deepseek 平台，也支持 NVIDIA NIM / OpenRouter / Fireworks / Novita / SGLang / vLLM / Ollama / OpenAI 兼容端点
5. **Thinking**：`Shift+Tab` 切 off → high → max
6. **1M 上下文**：受 DeepSeek API 限制，TUI 提供 prefix-cache 命中遥测
7. **持久 memory**：`DEEPSEEK_MEMORY=on` env 启用
8. **Skills 多目录扫描**：`.agents/skills`、`.claude/skills`、`.cursor/skills`、`~/.deepseek/skills`（**直接复用 Claude skills**）
9. **HTTP API + ACP**：`deepseek serve --http`（SSE）/ `deepseek serve --acp`（接 Zed 编辑器）

---

## 五、与 Claude Code 设计对比

| 维度 | DeepSeek-TUI | Claude Code |
|---|---|---|
| 设计哲学 | Codex/Claude Code 风格 agent loop | 同源 |
| 实现 | Rust + ratatui（键盘驱动 TUI） | TS/Node CLI |
| 模式 | Plan / Agent / YOLO 三档显式 | Default / `--dangerously-skip-permissions` |
| Skills | 扫多目录（含 `.claude/skills`，跨 agent 复用） | 原生 |
| Memory | `DEEPSEEK_MEMORY` opt-in 单文件 | `CLAUDE.md` + auto memory（更精细）|
| 子 agent | sub-agents + native `rlm_query` 批量 | Task tool |
| ACP | `deepseek serve --acp`（Zed） | 无 |
| HTTP API | `deepseek serve --http` SSE | 无 |
| 多 provider | 8+（含自托管 vLLM/SGLang/Ollama） | 仅 Anthropic |

**结论**：DeepSeek-TUI 在工具能力上**接近 Claude Code**，多 provider 和 ACP 服务端模式甚至**比 Claude Code 更开放**；记忆系统和 skill 生态是 Claude Code 更成熟。

---

## 六、定价（2026-05-08 实价，含 75% 折扣）

| 模型 | Cache hit | Cache miss | Output |
|---|---|---|---|
| **V4-Pro** | $0.003625/1M | $0.435/1M | **$0.87/1M** |
| **V4-Flash** | $0.0028/1M | $0.14/1M | **$0.28/1M** |

**⏰ 75% Pro 折扣截止**：**2026-05-31 UTC 15:59** — 5 月内重度试用最划算。

旧 alias `deepseek-chat` / `deepseek-reasoner` **将于 2026-07-24 弃用**，迁到 `deepseek-v4-pro` / `deepseek-v4-flash`。

---

## 七、已知坑（GitHub issues 一手）

- **#828**（open）多终端长会话**自动卡死** ← 第一次试用别选主仓
- **#774**（open，15 评论）卡在工作中无回复
- **#743**（open，12 评论）token 消耗显著增加
- **#1071**（open）fakeip 模式代理与 fetch_url 不兼容（普通 HTTP 代理 7897 不受影响）
- **#1060** "Stream stalled: no data received for 90s"

---

## 八、凯戈装机第一次跑通（2026-05-08）

✅ brew install + brew link --overwrite（踩坑 1+2）
✅ pip uninstall deepseek-cli（踩坑 2 触发原因）
✅ brew unlink && brew link（修复踩坑 2）
✅ hash -r（修复踩坑 3）
✅ deepseek doctor 全绿，**默认进 V4-Pro + thinking max**
✅ TUI 启动成功，4 步引导（欢迎/语言/信任目录/快捷键速记）
✅ 中文 UI 生效（Composer 提示"编写任务或使用 /"）
✅ Skills 自动检测到 `.claude/skills`（6 项）

**Skills 软链已配**（2026-05-08）：`~/.deepseek/skills/` 下已建 `greet-kaige` + `kb-ingestor` 软链指向 `~/.claude/skills/` 对应目录（`~/.claude/skills` 实际只 2 个真 skill，原以为 6 项是把 `.git/.gitignore/README.md/.DS_Store` 误数）。

---

## 九、实战表现实测（2026-05-08）

> **场景**：凯戈让 V4 跑 `~/magicode-website` 项目分析（500 行级静态站，Plan 只读模式），让 Claude（Opus 4）核对 V4 报告。

### 9.1 报告质量

V4 列出 3 处问题：
1. Apple Silicon 检测靠 `navigator.maxTouchPoints > 1` 不可靠
2. `navigator.userLanguage` 是 IE 死代码
3. i18n 通用 `[data-i18n-html]` 处理器后被硬编码 `replace('\n', '<br>')` 覆盖，未来重构会断裂

**Claude 核对结果**：
- ✅ **3 处全是真问题，0 处幻觉**
- ⚠️ 行号系统性偏移（+9 / +1 / +3~5）— 代码片段引用准确，但行号数错
- 问题 3 那个"未来重构会断裂"的推理 **接近 Claude / Codex 的洞察深度**

### 9.2 反思力实测

凯戈把 Claude 评价转给 V4，问"为什么所有行号都偏？是不是读了缓存？"

V4 回应：
- **承认错误**，给出三个偏移量明细表
- **根因诊断**：`read_file` 返回纯文本不带行号 → 手工数行精度不足
- **排除系统性偏移假设**：偏移量方向不同（+9 vs +1 vs +3~5）反证不是缓存/旧版本
- **可操作改进**：下次用 `grep_files` 拿行号，不裸眼数

| 反思维度 | 评分 |
|---|---|
| 承认错误 | ⭐⭐⭐⭐⭐ 直接认账，不甩锅 |
| 根因诊断 | ⭐⭐⭐⭐⭐ 用偏移方向反证排除假设 |
| 可操作改进 | ⭐⭐⭐⭐ 具体工具 + 具体场景 |
| 反思深度 | ⭐⭐⭐⭐ 定位到工具层根因，不是粗心 |

**对比 Gemini**：被指出错误后 Gemini 倾向"补论据/扩展讨论"，V4 直接走"承认 + 根因 + 改进"三件套，**接近 Claude / Codex 反思质量**。

### 9.3 工具链定位（实测后定型）

✅ **第二审视角**（Plan / 只读模式）：替代或并行 Gemini 做代码 review
✅ **价格优势**：V4-Pro 输出 $0.87/1M（Claude Opus 的 ~1/15），75% 折扣 5-31 到期
✅ **不替代 Claude Code 主战**：但**值得长期保留**作为审查/对账层
⚠️ **行号建议用 grep 校验**：写入 KB 前先 `grep -n` 验一下，避免下游引用错误

---

## 十、来源

- [Hmbown/DeepSeek-TUI](https://github.com/Hmbown/DeepSeek-TUI)（仓库）
- [GitHub API: Hmbown/DeepSeek-TUI](https://api.github.com/repos/Hmbown/DeepSeek-TUI)（实证 stars/forks）
- [deepseek-ai/awesome-deepseek-agent](https://github.com/deepseek-ai/awesome-deepseek-agent)（官方收录）
- [Simon Willison · DeepSeek V4 — almost on the frontier, a fraction of the price](https://simonwillison.net/2026/apr/24/deepseek-v4/)
- [Artificial Analysis: DeepSeek V4-Pro](https://artificialanalysis.ai/models/deepseek-v4-pro)（Intelligence Index 52，排第 3）
- [HN #47884971: DeepSeek v4](https://news.ycombinator.com/item?id=47884971)（2091 赞 / 1607 评论）

## 十一、关联词条

- [[deepseek-v4]] — V4 模型本身的研究
- [[deepseek-v4-pricing]] — V4 定价细节
- [[codex-cli]] — 另一个 agentic CLI（GPT-5.5 后端）
- [[skills]] — Skills 系统（DeepSeek-TUI 复用 `.claude/skills`）
- [[claude-code/]] — Claude Code 工具链
