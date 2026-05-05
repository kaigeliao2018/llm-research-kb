# LLM Research KB — 研究日志

## 2026-04-21

- 项目初始化
- 起因：调研智谱清言 GLM Coding Plan，可将 Claude Code 接入 GLM 模型
- 参考文档：
  - GLM-5.1 接入指南：https://docs.bigmodel.cn/cn/coding-plan/using5-1
  - Claude Code 接入方案：https://docs.bigmodel.cn/cn/coding-plan/tool/claude
- 后续：整理 GLM Coding Plan 可行性分析 → wiki/zhipu-glm-coding-plan.md

## 2026-04-24

- DeepSeek V4 Preview 发布当日研究，产出 `wiki/deepseek-v4.md`
- 信息源（5 个）交叉核对：官方公告 + HF 模型卡（Pro/Flash）+ Simon Willison 评测 + Fello AI 综述
- 核心结论：
  - V4-Pro（1.6T/49B）与 V4-Flash（284B/13B）双变体，MIT license
  - 1M context 原生；效率相比 V3.2 大幅改善（Pro: 27% FLOPs, 10% KV cache）
  - 编程最强（Codeforces 3206，LiveCodeBench 93.5%），知识/通识略逊 Gemini 3.1 Pro
  - 价格极低：Flash $0.14/$0.28，Pro $1.74/$3.48
  - 兼容 OpenAI + Anthropic API 协议；已集成 Claude Code / OpenClaw / OpenCode
  - 老模型 deepseek-chat / deepseek-reasoner 将于 2026-07-24 下线
- 待核实：
  - DSA 与 CSA+HCA 的术语对应关系（需读 tech report PDF）
  - 官方 pricing 页面与 Simon 数字的一致性

### 2026-04-24 下午 · 网页版外勤启动

- 开通网页端 Claude Projects "llm-research-kb"，Mac 本地 ↔ 网页版双端分工确立
  - Mac（Claude Code）：写文件、git、接入实测
  - 网页版：读 PDF、爬中文评测、搜集多源信息
- Mac → 网页版已交付：Project 描述、Custom Instructions、第一轮同步信息
- 网页版首轮回执确认，要求补素材后开工

### 素材准备状态
- ✅ 凯戈手工下载 2 份 PDF 至桌面：
  - `~/Desktop/DeepSeek_V4.pdf`（58 页，4.5MB，官方技术报告）
  - `~/Desktop/DeepSeek+V4—almost+on+the+frontier,+a+fraction+of+the+price.pdf`（5 页，Simon Willison 博客存档）
- ✅ Mac 已写好 Task 1 给网页版的 prompt（本对话内，待凯戈出门归来复制）
- ⏸ Task 1 尚未下发给网页版（凯戈需手工拖 PDF + 粘贴 prompt）
- ⏸ Task 2（pricing 核对）、Task 3（Claude Code × DeepSeek V4 接入）待 Task 1 收工后并行

### 收工状态（凯戈出门前）
- 本地无未 commit 变更？待确认（本轮未 git push）
- 30min push 提醒：本轮未启用（开场流程被打断）

### 2026-04-24 晚 · Task 1 回执入库

- 网页版交付 DSA/CSA/HCA 术语考据，产出 `wiki/deepseek-v4-dsa-clarification.md`
- **关键反转**：CSA/HCA 是 V4 **官方术语**（不是第三方重命名），DSA 是 V3.2 旧术语，被 CSA 作为第二步调用
- 三者层级：`CSA = KV 压缩 → DSA`；`HCA = 更狠的压缩 + dense attention`；`DSA ⊂ CSA`
- 修正：`wiki/deepseek-v4.md` 第 3.1 节整段重写，改掉"DSA 是 V4 核心创新"的错误表述
- 入库 PDF：`resources/raw/DeepSeek_V4.pdf`（58页官方）+ `resources/raw/DeepSeek_V4_almost_on_the_frontier.pdf`（Simon 博客归档）
- 网页版提问："要不要让我查 Fello AI 那篇确认 'CSA+HCA' 的二手来源？" → 待凯戈定

## 2026-05-05 · wiki/claude-code/ Skills 主题入库

### 起因
凯戈看了一个 8 分钟视频"Claude Code 顶级 Skills 15 选"，让 Gemini 转文字、网页端 Claude 做了一份分析。要求 CLI Claude 二次审查 + 整合为 wiki + 课件素材。

### 产出
- 第 7 篇 `skills.md` v1（commit `8069a98`）—— 视频案例 + 项目矩阵 + 课件大纲（基于初步认知）
- 派 3 个 agent 并行核对官方源后，发现 v1 有错（webapp-testing 误判为社区 Skill）+ 大量漏报
- 第 7 篇 `skills.md` v2 + 第 8 篇 `skills-howto.md`（commit `421ddf8`）

### 关键发现
1. Anthropic 官方 Skill 总数 **17 个**（仓库 128k★ 已核），视频只列 5 个真官方 + 10 个误归类
2. 官方 MCP 生态修正：GitHub MCP（GitHub 官方）+ Cloudflare MCP（CF 官方）+ Playwright MCP（微软官方）—— 都对凯戈高匹配，视频未提
3. 视频漏报对凯戈最有价值的官方 Skill：`xlsx`（RobotEvents 数据） / `claude-api` / `doc-coauthoring` / `mcp-builder` / `pptx` / `canvas-design`
4. 网页端 Claude 分析编造了大量项目细节（"Day 4"、"LEVEL3 框架"、"四 Agent 架构"），保留为课件反面教材

### 实施路径（三阶段）
- 阶段 A（本周）：装 anthropics/skills marketplace + 实测 `pdf` 解析 vex-iq-kb 新赛季 Game Manual v0.1，对比 fitz
- 阶段 B（按触发条件）：未来项目储备（webapp-testing / frontend-design / GitHub MCP 等）
- 阶段 C（持续）：沉淀为知识库 + 教学课件（已规划三课时拆分）

### Push 状态
- 两个 commit 已落本地（`8069a98`、`421ddf8`）
- v1 已成功 push（`798aa92..8069a98`）
- v2 push 时 GitHub 连接超时（2026-05-05 晚）
- ⏳ **下次上线动作**：网络恢复时 `cd ~/llm-research-kb && git push`

---

## 2026-05-05 下午 · Skills 主题第二轮：网页版 KB 事实核对 + 生态地图归并

### 起因
凯戈在网页端让 Claude（Opus 4.7）做"Claude Skills 深度搜索"，产出 `~/Desktop/claude-skills-kb.md`（22k 字，12 节，60+ 来源声称），并附"交接 brief"建议直接归档到 `magikid-projects-hub/knowledge-assets/`。

按研究铁律 + Inventory Before Output，CLI Claude 拒绝直接归档，先做事实核对。

### 第一轮预查（目录幻觉）
- brief 声称"`magikid-projects-hub/knowledge-assets/` 已存在，与四份战略文档同级"
- 实测：`magikid-projects-hub/` 存在，**knowledge-assets/ 子目录不存在**；LEVEL3-GOVERNANCE 等文件全盘 0 命中
- 与昨天 `skills.md` §4.3 记录的网页版幻觉模式**完全一致**

### 三个 agent 并行核对 5 项数据点
| # | 声称 | 判定 |
|---|---|---|
| 1 | Superpowers 178k stars | ✅ 实测 178,455 |
| 2 | 2026-01-15 进官方 marketplace | ✅ PR #148 精确 |
| 3 | 2025-12-18 开放标准 + agentskills.io | ✅ |
| 3b | 跨平台采纳列表 Codex/Gemini CLI/Antigravity/OpenCode/Windsurf | ❌ 发布日点名是 MS/OpenAI/Atlassian/Figma/Cursor/GitHub |
| 4 | Smithery 756 次基准 | ❌ **张冠李戴**：一手是 ScaleKit 75 次；Smithery 自己另有 756 次且**结论相反** |
| 4b | 数字 1365/4724/44026 | ✅ 来自 ScaleKit |
| 4c | "省 17 倍" | ❌ 原文 10-32× |
| 5 | Simon 文章 2025-10-16 | ✅ |

### 三大硬伤
1. **Smithery / ScaleKit 张冠李戴**（最严重）—— 中文博客 trashwbin.top 把两份独立研究混为一谈
2. **开放标准跨平台名单错位** —— 发布日合作方非 KB 所列
3. **Superpowers 安装命令仓库名错** —— 真实是 `claude-plugins-official`

### 产出
- ✅ `wiki/claude-code/skills-factcheck-2026-05-05.md`（commit `d78e8ae`）—— 研究痕迹
- ✅ `wiki/claude-code/skills-ecosystem.md`（第 9 篇）—— 生态地图：吸收网页版 KB 70% 可保留部分 + 修正 3 大硬伤 + 删项目幻觉
- ✅ `skills.md` §10 产物清单更新

### 网页版幻觉模式诊断
- 跨会话复发——昨天的修正没写入网页版上下文
- 项目细节"贴身化"是结构性问题，不是单次失误
- **教训再次确认**：必须拿真实 INDEX 对账，否则建议建立在沙堆上

### 下一步
- 阶段 A：装 anthropics/skills marketplace + 实测 `pdf` Skill 解析新赛季 Game Manual v0.1，对比 fitz
- 桌面 `claude-skills-kb.md` 处理：归档为 raw 素材或删除（凯戈定）
