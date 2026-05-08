---
name: Gemini 15 Skills 视频解析审核
description: 对 Gemini 输出的"Claude Code 顶级 Skills 实战全清单 (15选)"视频解析报告做全网交叉核对，发现 ~60% 具体声称有错（MCP 误标 Skill / 数字幻觉 / 名字幻觉 / 框架与子技能混淆）。第二份"二手 AI 输出"幻觉模式研究痕迹
type: research-trace
date: 2026-05-06
related: skills.md / skills-ecosystem.md / skills-factcheck-2026-05-05.md
---

# Gemini 15 Skills 视频解析审核（2026-05-06）

> 研究日期：2026-05-06
> 核对对象：Gemini 输出的"Claude Code 顶级 Skills 实战全清单（15选）"（基于 8 分 30 秒视频整理）
> 核对人：CLI Claude（Opus 4）+ 1 个 general-purpose agent 并行核对
> 配套：[[skills-factcheck-2026-05-05]]（昨天的网页版 KB 核对，第 1 份痕迹）

---

## 一、起因

凯戈拿到 Gemini 对一段 8.5 分钟视频的整理稿，让 CLI Claude 以"资深 AI 专家"身份审核内容真实性。

按研究铁律（严禁猜想、二手转述只当线索不当事实）+ Inventory Before Output（产出引用外部素材的成品前先列对账表），CLI Claude 做了全网交叉核对。

## 二、Gemini 报告的 15 个声称（Raw）

报告把 Skills 分三梯队：

**第一梯队（基础）**：agent-browser / Superpowers (96k+ stars) / PDF Processing / Tavily

**第二梯队（效率）**：Frontend Design (277k+ installs) / Context7 / Systematic Debugging / Marketing Skills (20+) / tmux / Skill Creator / Webapp Testing

**第三梯队（专业）**：DOCX / Changelog Maintenance / GPT Researcher / Code Refactoring

收尾建议 4 条：分批安装 / 定期清理 / 看重出身 / 学会组合（这部分论点合理）。

## 三、核对结果汇总（15 项）

| # | 声称名 | Gemini 标签 | 真实身份 | 判定 |
|---|---|---|---|---|
| 1 | agent-browser | "强烈推荐 Skill" | 无此名项目；最接近 `microsoft/playwright-mcp`（MCP 服务器）或社区 `lackeyjb/playwright-skill` | 🔴 **幻觉名字** |
| 2 | Superpowers | "GitHub 96k+ stars" | `obra/superpowers` 实测 **179,451 stars**（GitHub API，2026-05-06）；且为 14 子技能框架，非单 Skill | 🔴 **数字差 ~83k + 类别错** |
| 3 | PDF Processing | Anthropic 官方 | `anthropics/skills/document-skills/pdf` | 🟢 准确 |
| 4 | Tavily | "AI 搜索 Skill" | `tavily-ai/tavily-mcp` — **MCP 服务器**，不是 Skill | 🔴 **类别错（MCP ≠ Skill）** |
| 5 | Frontend Design | "277k+ 安装量" | Skill 真实存在（官方）；277k 数字仅见于 2026 年 Medium 文章，**Anthropic 官方未公布** | 🟡 Skill ✅ / 数字三方未证 |
| 6 | Context7 | "实时文档 Skill" | `upstash/context7` — **MCP 服务器**，~54.5k stars | 🔴 **类别错（MCP ≠ Skill）** |
| 7 | Systematic Debugging | "独立 Skill" | 实为 `obra/superpowers/skills/systematic-debugging` 子技能 | 🟡 内容 ✅ / 非顶层 Skill |
| 8 | Marketing Skills | "20+ skills 包" | 多个竞争仓库（alirezarezvani 43 / OpenClaudia 63 / kostja94 160+ / BrianRWagner 19），**没有恰好"20+"的包** | 🟡 类目存在 / 具体包对不上 |
| 9 | tmux | "稳定器 Skill" | tmux 是 20 年历史的 Unix 终端复用器（Nicholas Marriott），**不是 Skill，与 Claude 无关** | 🔴 **类别错（CLI 工具 ≠ Skill）** |
| 10 | Skill Creator | Anthropic 官方元 Skill | `anthropics/skills/document-skills/skill-creator` | 🟢 准确 |
| 11 | Webapp Testing | Anthropic 官方 | `anthropics/skills/document-skills/webapp-testing`（Playwright） | 🟢 准确 |
| 12 | DOCX | Anthropic 官方 | `anthropics/skills/document-skills/docx` | 🟢 准确 |
| 13 | Changelog Maintenance | "自动维护更新日志 Skill" | `ComposioHQ/awesome-claude-skills/changelog-generator` — 社区 Skill，**非 Anthropic 官方** | 🟡 Skill 存在 / 非官方 |
| 14 | GPT Researcher | "深度研究 Skill" | `assafelovic/gpt-researcher` — 独立 Python 项目，~26.9k stars，**无 SKILL.md** | 🔴 **类别错（Python 项目 ≠ Skill）** |
| 15 | Code Refactoring | "代码坏味道 Skill" | 多个不相关社区尝试（l-mb / finereli / opkod-france 等），**无定型 "Code Refactoring" Skill** | 🟡 名字模糊 |

## 四、统计

| 类别 | 计数 |
|---|---|
| 完全准确 | **5** / 15（33%） |
| 类别错（MCP / CLI / Python 项目误标 Skill） | **6** / 15 |
| 数字幻觉 | **3** / 15 |
| 完全编造（幻觉名字 / 错误归属） | **1** / 15 |

## 五、错误根因分析

| 错误类型 | 命中条目 | 机理 |
|---|---|---|
| **MCP ↔ Skill 混淆** | #1, #4, #6（Playwright/Tavily/Context7） | 两者都"扩展 Claude Code"，但架构层不同。**Skill = 行为/知识层；MCP = 连接/认证层**，互补不互换 |
| **框架 ↔ 单 Skill 混淆** | #2, #7（Superpowers + 其子技能） | Superpowers 是 14 子技能框架，不是"一个 Skill" |
| **独立工具 ↔ Skill 混淆** | #9, #14（tmux, GPT Researcher） | 幻觉与 Claude 生态的归属关系 |
| **Stars/install 数字幻觉** | #2（96k vs 真实 179k）, #5（277k 未证） | LLM 编造合理量级；与昨天 [[skills-factcheck-2026-05-05]] §3.2 同模式 |
| **幻觉名字** | #1（agent-browser） | 编造听起来合理但不映射到任何真实项目的名称 |

## 六、与昨日（网页版 KB 核对）的横向对比

| 维度 | 昨日 网页版 Claude（Opus 4.7）KB | 今日 Gemini 视频解析 |
|---|---|---|
| 总声称量 | 60+ 来源声称（22k 字） | 15 个 Skill + 收尾建议 |
| 主要错误 | ScaleKit/Smithery 张冠李戴 / 跨平台名单错位 / Superpowers 安装命令错 | MCP 误标 Skill（×3）/ 数字幻觉（×3）/ 幻觉名字（×1）|
| 项目细节幻觉 | ✅ 重发（VEX-IQ-KB Day 4 等不存在的术语） | ⚠️ 较轻（仅尾段"建议你做 VEX 用"） |
| 准确率 | ~70% 可保留 | ~33% 完全准确 |
| 共同模式 | **二手 AI 输出对生态架构层（Skill/MCP/CLI/项目）边界感不足，且对量化数字（stars/installs/次数）有强烈编造倾向** | 同 |

**模式确认**：跨 LLM、跨产品形态（网页 KB / 视频解析）、跨会话——二手 AI 输出对**新生态术语 + 量化数字**的幻觉是**结构性问题**，单点修正不解决根因。

## 七、教学建议（凯戈给学生）

可作教学案例使用。核心 takeaway：

1. **Skill ≠ MCP ≠ CLI 工具 ≠ 独立项目**——四种"扩展 Claude Code"的方式架构层不同，混淆导致安装命令错、期望错、架构决策错
2. **GitHub stars 和 install 数字是幻觉重灾区**——务必直接 `api.github.com/repos/{owner}/{repo}` 验证
3. **框架内的子技能不是独立 Skill**——装一个 `superpowers` 同时获得全部 14 子技能，不能单独寻址
4. **Anthropic 官方 Skill 总数恰好 17 个**（截至 2026-05-06）；超出此名单的都是社区
5. **二手 AI 输出（视频/网页 KB）只当线索不当事实**——必须查一手来源（feedback_ai_hype_filter 铁律）

**对原报告的信任分数**：**4/10** — 方向性的入门清单可看，但 ~60% 具体声称需要纠正后才能行动。

## 八、附录：核对方法

派 1 个 general-purpose agent 并行核对 15 项声称，工具组合：

- **WebSearch**：搜索项目名 + "claude skill" 限定
- **WebFetch**：访问 GitHub 仓库 README、Anthropic blog、声称的 Medium 文章
- **GitHub API**：`api.github.com/repos/{owner}/{repo}` 实测 stars
- **本地交叉核对**：
  - `~/.claude/plugins/marketplaces/anthropic-agent-skills/skills/` 实际安装目录（17 项官方清单基线）
  - `~/llm-research-kb/wiki/claude-code/skills-ecosystem.md` 已核对的生态地图

## 九、来源（核对过的）

- [anthropics/skills](https://github.com/anthropics/skills) — 17 官方 Skills 名单基线
- [api.github.com/repos/obra/superpowers](https://api.github.com/repos/obra/superpowers) — Superpowers 实时 stars
- [github.com/upstash/context7](https://github.com/upstash/context7) — MCP 服务器（非 Skill）
- [github.com/tavily-ai/tavily-mcp](https://github.com/tavily-ai/tavily-mcp) — MCP 服务器（非 Skill）
- [github.com/assafelovic/gpt-researcher](https://github.com/assafelovic/gpt-researcher) — 独立 Python 项目（非 Skill）
- [github.com/microsoft/playwright-mcp](https://github.com/microsoft/playwright-mcp) — MCP 服务器（agent-browser 真身候选）
- [ComposioHQ/awesome-claude-skills](https://github.com/ComposioHQ/awesome-claude-skills) — Changelog generator 社区 Skill
- 内部对照：[[skills-factcheck-2026-05-05]]（昨日核对痕迹）

## 十、关联词条

- [[skills]] — Skills 实战指南
- [[skills-howto]] — Skill 操作手册
- [[skills-ecosystem]] — Claude Skills 生态地图
- [[skills-factcheck-2026-05-05]] — 第 1 份"二手 AI 输出"幻觉模式核对痕迹
