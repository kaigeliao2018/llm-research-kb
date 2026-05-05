---
name: claude-skills 网页版 KB 事实核对
description: 对 2026-05-05 网页版 Claude（Opus 4.7）交付的 claude-skills-kb.md 进行 5 项关键数据核对的研究痕迹，发现 3 处硬伤 + 1 处项目细节幻觉重发
type: research-trace
date: 2026-05-05
related: skills.md / skills-howto.md / SOP_MULTI_AGENT_VERIFICATION
---

# Claude Skills 网页版 KB 事实核对（2026-05-05）

> 研究日期：2026-05-05
> 核对对象：网页版 Claude（Opus 4.7）交付的 `claude-skills-kb.md`（22k 字，12 节，60+ 来源声称）
> 核对人：CLI Claude（Opus 4）+ 3 个并行 general-purpose agent
> 配套：[[skills]] / [[skills-howto]]

---

## 一、起因

凯戈在网页端让 Claude（Opus 4.7）做"Claude Skills 深度搜索"，产出 `claude-skills-kb.md` 落到 `~/Desktop/`。网页版同时附了一份"交接 brief"给 CLI Claude，建议直接归档到 `magikid-projects-hub/knowledge-assets/`。

按研究铁律（严禁猜想）+ Inventory Before Output（产出引用外部素材的成品前先列对账表），CLI Claude 拒绝直接归档，先做事实核对。

## 二、第一轮预查：目录结构幻觉

网页版 brief 第 1 段声称：
> 目标路径：`magikid-projects-hub/knowledge-assets/claude-skills-kb.md`。该目录已存在（与 KNOWLEDGE-ASSETS-README.md、STRATEGIC-COMMERCIALIZATION-ROADMAP.md 等四份战略文档同级）。

实测：
- `magikid-projects-hub/` 目录存在 ✅
- `knowledge-assets/` 子目录 ❌ **不存在**
- `find` 全盘扫 LEVEL3-GOVERNANCE / STRATEGIC-COMMERCIALIZATION / KNOWLEDGE-ASSETS：**0 命中**

**结论**：与昨天 `skills.md` §4.3 记录的「网页版幻觉化项目细节」是同一种复发——**这是模式问题，不是单次失误**。

## 三、5 项数据点并行核对

派 3 个 general-purpose agent 并行核对 5 项关键声称（见附录 A 完整 prompt）。

### 3.1 核对结果汇总

| # | 声称（出自网页版 KB） | 判定 | 实际值 / 证据 |
|---|---|---|---|
| 1a | obra/superpowers stars 数 177,600+ | ✅ 准确 | GitHub API 实测 **178,455**（2026-05-05） |
| 1b | 过去 30 天增加 41,600+ stars | ❓ 量级合理 | GitHub Stargazers API 40k 上限截断，无法独立精确证实 |
| 2 | 2026-01-15 进 Anthropic 官方 marketplace | ✅ **完全准确** | PR #148 commit 时间 2026-01-15 02:03:36 UTC，仓库为 `anthropics/claude-plugins-official` |
| 3a | 2025-12-18 Anthropic 发布 Agent Skills 开放标准 | ✅ 准确 | Anthropic 官方自称 open standard；agentskills.io 真实存在 |
| 3b | 跨平台支持：Codex / Gemini CLI / Cursor / Antigravity / OpenCode / Windsurf | ⚠️ **错** | 发布日官方点名的是 **Microsoft / OpenAI / Atlassian / Figma / Cursor / GitHub**；其他 CLI 是后续陆续加入，不是同步采纳 |
| 4a | Smithery 做了 756 次基准测试 | ❌ **张冠李戴** | 一手是 **ScaleKit 的 75 次**（不是 Smithery，不是 756）；Smithery 自己另有一份独立基准且**结论相反** |
| 4b | token 数字 CLI 1,365 / CLI+Skills 4,724 / MCP 44,026 | ✅ 数字准确 | 来源是 ScaleKit（不是 Smithery） |
| 4c | "CLI 比 MCP 省 17 倍 token" | ❌ 偏差 | ScaleKit 原文是 **10-32×**（视任务） |
| 5a | Simon Willison 文章 2025-10-16，标题"bigger deal than MCP" | ✅ 准确 | URL/日期/标题全对 |
| 5b | X 浏览 422k | ❓ 不可证伪 | 量级合理 |

### 3.2 三大硬伤详述

#### 硬伤 1：Smithery / ScaleKit 张冠李戴（最严重）

网页版 KB §3.3 写：
> Smithery / ScaleKit 基准测试 ··· 测试规模：756 次 benchmark

事实：
- **ScaleKit**（scalekit.com）做了 **75 次**（25 样本 × 3 接口），代码开源在 [scalekit-inc/mcp-vs-cli-benchmark](https://github.com/scalekit-inc/mcp-vs-cli-benchmark)
- **Smithery** 做了**另一份独立的 756 次基准**（3 API × 8 实验族），结论是 **MCP 91.7% vs CLI 83.3%（MCP 反而更可靠）**
- 中文博客 trashwbin.top 把两家研究混为一谈，直接被网页版抄进 KB

**影响**：KB §3.3 不仅数字错（75 vs 756），**结论方向也错**——Smithery 真实结论与 ScaleKit 相反，不能被同一段当佐证。

#### 硬伤 2：开放标准跨平台采纳名单错位

网页版 KB §0 + §11 列：
> Claude Code、Codex、Gemini CLI、Cursor、Antigravity、OpenCode、Windsurf 都已支持

事实：
- 2025-12-18 发布日官方点名的合作方是 **Microsoft / OpenAI / Atlassian / Figma / Cursor / GitHub**（VentureBeat / The New Stack 多源确认）
- Codex CLI / Gemini CLI / Antigravity / OpenCode / Windsurf 是后续 48 小时到 2026-03 期间陆续加入
- 用 KB 这个清单跟人说 "发布即跨这 7 个 CLI 都支持"——失实

#### 硬伤 3：Superpowers 安装命令可能错（待二次确认）

网页版 KB §6.2 写：
```
/plugin marketplace add obra/superpowers-marketplace
/plugin install superpowers@superpowers-marketplace
```

事实：
- 官方 marketplace 仓库是 `anthropics/claude-plugins-official`（不是 obra 的 superpowers-marketplace）
- 实际安装应当走：`/plugin install superpowers@claude-plugins-official`
- obra/superpowers README 自己也是这么说的："Superpowers is available via the official Claude plugin marketplace"

**注意**：obra 也可能维护了独立的 obra/superpowers-marketplace 仓库供非官方安装。这一点需要后续直接 `gh repo view` 确认，不要直接抄 KB 的命令。

## 四、项目细节幻觉重发

网页版 KB 与昨天 `skills.md` §4.3 记录的幻觉模式**完全一致**：

| 网页版 KB 引用的项目细节 | 实际情况（INDEX.md 对账） |
|---|---|
| "VEX-IQ-KB Day 4"（KB §7.1 / §12.2 / §12.4 / brief 第 2 段）| vex-iq-kb 没有 "Day N" 迭代结构；当前阶段是新赛季 Level Up 启动（2026-05-01） |
| "LEVEL3-GOVERNANCE-FRAMEWORK"（KB §7.1 / §7.2 / §12.4）| 不存在该文件；项目治理走 CLAUDE.md + system-prompt + feedback memory |
| "magikid-projects-hub 集中管理 6 项目"（KB §7.1）| hub 是"按需"，活跃项目 8+ |
| "knowledge-assets/ 与四份战略文档同级"（brief）| 子目录不存在 |

**模式诊断**：
- LLM 拿到片段信息后，会**幻觉化项目细节**让建议看起来"贴身"
- 跨会话复发——昨天的修正没有写入网页版的训练或上下文
- **教训（再次确认）**：必须拿真实 INDEX 对账，否则建议建立在沙堆上

## 五、网页版 KB 的可保留价值

事实核对发现 ~30% 数据点有错，但其余 70% 可保留：

| 价值点 | 评估 |
|---|---|
| 17 个官方 Skill 完整名单 | ✅ 与昨天 3-agent 核对结果一致，可保留 |
| Tier S/A/B 社区仓库矩阵（VoltAgent / travisvn / karanb192 / ComposioHQ / sickn33 / BehiSecc / alirezarezvani / daymade）| ✅ 多源验证可信，是昨天 skills.md 没覆盖的生态广度 |
| Simon Willison / Armin Ronacher / Jesse Vincent / Ruben Hassid 业界观点 | ✅ 来源真实，论点有据 |
| 三大聚合站（claudemarketplaces / awesomeclaudeskills / claudepluginhub）| ✅ 网页可达 |
| Skills × MCP × CLI 三层架构图（KB §9）| ✅ 概念图正确，与 skills-howto §5 决策树互补 |
| 安全 + 工程 + 工作流避坑清单（KB §8）| ✅ 多数条目可信，但需删除"Linux 路径 bug"等未核实条目 |
| 时间线（KB §11）| ⚠️ 部分日期需核 |
| 项目矩阵（KB §7）| ❌ 项目细节幻觉，整段重写 |

## 六、归档建议

### 不归档到原建议位置

- 原建议：`magikid-projects-hub/knowledge-assets/claude-skills-kb.md`
- 拒绝理由：
  1. 子目录不存在（建立要先决策）
  2. 网页版 KB 自身有硬伤，不能原样落盘
  3. `llm-research-kb/wiki/claude-code/` 已经是成型的 Claude Code KB（8 篇），主题更顺

### 改写后归并到 `llm-research-kb/wiki/claude-code/`

建议产出 **第 9 篇 `skills-ecosystem.md`**，吸收网页版 KB 的生态广度部分：
- 修正硬伤 1-3
- 砍掉项目细节幻觉
- 与 skills.md / skills-howto.md 互不重叠（前者是案例评估，后者是操作手册，新篇专门讲生态地图）

### 沉淀本核对报告

- 本文件 `skills-factcheck-2026-05-05.md` 留作研究痕迹
- 在 skills.md §10 "本次评估产物" 追加引用
- 在 SOP_MULTI_AGENT_VERIFICATION 的幻觉记录追加（`karpathy-kb-research/` 那边）

## 七、附录

### A. 三个核对 agent 的任务划分

| Agent | 核对范围 | 工具 |
|---|---|---|
| Agent 1 | obra/superpowers stars + 进官方 marketplace 时间 | WebFetch GitHub API + claude-plugins-official 仓库 |
| Agent 2 | agentskills.io 开放标准 + Simon Willison 文章 | WebFetch + WebSearch（Anthropic 公告/SiliconANGLE/VentureBeat/The New Stack） |
| Agent 3 | Smithery vs ScaleKit 基准来源 | WebFetch（trashwbin.top / scalekit.com / smithery.ai） |

### B. 核心来源

- [obra/superpowers GitHub API](https://api.github.com/repos/obra/superpowers)
- [anthropics/claude-plugins-official](https://github.com/anthropics/claude-plugins-official)
- [Anthropic — Equipping agents with Agent Skills](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)
- [agentskills.io/home](https://agentskills.io/home)
- [SiliconANGLE — Anthropic makes agent Skills an open standard](https://siliconangle.com/2025/12/18/anthropic-makes-agent-skills-open-standard/)
- [VentureBeat — Anthropic launches enterprise Agent Skills](https://venturebeat.com/ai/anthropic-launches-enterprise-agent-skills-and-opens-the-standard)
- [The New Stack — Agent Skills: Anthropic's Next Bid](https://thenewstack.io/agent-skills-anthropics-next-bid-to-define-ai-standards/)
- [Simon Willison — Claude Skills are awesome](https://simonwillison.net/2025/Oct/16/claude-skills/)
- [ScaleKit: MCP vs CLI Benchmark](https://www.scalekit.com/blog/mcp-vs-cli-use)
- [scalekit-inc/mcp-vs-cli-benchmark](https://github.com/scalekit-inc/mcp-vs-cli-benchmark)
- [Smithery: MCP vs CLI Is the Wrong Fight (756 runs)](https://smithery.ai/blog/mcp-vs-cli-is-the-wrong-fight)
- [trashwbin.top 中文二手转载](https://blog.trashwbin.top/en/posts/cli-vs-mcp-vs-skills/)（已识别为张冠李戴源头）

## 八、关联词条

- [[skills]] — Skills 实战指南（§4.3 网页版幻觉案例首次记录）
- [[skills-howto]] — Skill 操作手册
- [[overview]] — Claude Code 整体架构
