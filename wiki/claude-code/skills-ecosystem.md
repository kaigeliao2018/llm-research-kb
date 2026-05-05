---
name: Claude Skills 生态地图
description: Claude Skills 生态全景：官方资源 + 头部社区项目 + 业界观点 + 三层架构 + 避坑清单。基于网页版 KB 改写归并 + skills-factcheck-2026-05-05 修正版
type: knowledge-map
date: 2026-05-05
related: skills.md / skills-howto.md / skills-factcheck-2026-05-05.md
---

# Claude Skills 生态地图

> 研究日期：2026-05-05
> 状态：生态全景（与 skills.md 案例评估、skills-howto 操作手册互补）
> 来源：网页版 KB 70% 可保留部分 + 三大硬伤修正
> **配套**：[[skills-factcheck-2026-05-05]] 是本文事实核对的研究痕迹

---

## 一、一页速览（事实核对版）

**Skill 是什么**：一个文件夹，含 `SKILL.md`（YAML frontmatter + Markdown），可选 scripts/、references/、assets/。**2025-10-16** Anthropic 正式发布；**2025-12-18** 升级为开放标准（agentskills.io）。

**它解决的问题**：渐进式加载（progressive disclosure）。每 Skill 启动 ~100 token（仅 metadata），命中 description 才加载正文（通常 < 5k token）。

**和 MCP 的关系**：互补，不互斥。**Skill = 行为/知识层，MCP = 连接/认证层，CLI = 动作层**。三者协同。

**跨平台采纳**：
- 2025-12-18 发布日官方点名合作方：**Microsoft / OpenAI / Atlassian / Figma / Cursor / GitHub**
- 后续陆续加入：Codex CLI / Gemini CLI / Antigravity / OpenCode / Windsurf 等
- ⚠️ 不要混淆"发布日采纳"与"后续生态扩张"——见 [[skills-factcheck-2026-05-05]] §3.2 硬伤 2

**安全提醒**：Skill 可执行任意代码，**只装可信来源**。

---

## 二、Anthropic 官方资源矩阵

### 2.1 文档/规范站点

| 资源 | 链接 | 用途 |
|---|---|---|
| 开放标准官网 | https://agentskills.io | 跨平台 Skill 标准定义、SDK |
| 官方仓库 | https://github.com/anthropics/skills | 17 个官方 Skill 源码（128k+ ★） |
| 工程博客发布稿 | https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills | 设计原理（progressive disclosure） |
| Agent Skills API 文档 | https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview | API 调用规范 |
| Claude Code Skills 文档 | https://code.claude.com/docs/en/skills | CC 中创建/管理 Skill |
| 官方插件市场仓库 | https://github.com/anthropics/claude-plugins-official | **官方 marketplace 真实仓库名（≠ skills 仓库）** |
| 创建 Skill 完整指南 PDF | resources.anthropic.com/.../The-Complete-Guide-to-Building-Skill-for-Claude.pdf | 28 页详细文档 |

### 2.2 17 个官方 Skill（已三 agent 核对）

详细全表见 [[skills]] §2，按用途分类：
- **文档处理**（4）：pdf / docx / pptx / xlsx
- **前端设计**（6）：frontend-design / web-artifacts-builder / canvas-design / theme-factory / brand-guidelines / algorithmic-art
- **测试开发**（3）：webapp-testing / claude-api / mcp-builder
- **写作协作**（2）：doc-coauthoring / internal-comms
- **元工具创意**（2）：skill-creator / slack-gif-creator

> 凯戈适配：高匹配 9 个，详见 [[skills]] §2

---

## 三、头部社区项目

### 3.1 Tier S — 头牌（必关注）

#### obra/superpowers
- **作者**：Jesse Vincent（Prime Radiant）
- **Stars**：178k+（2026-05-05 实测 GitHub API），2025-10-09 创建
- **官方背书**：✅ **2026-01-15 已被收录进 Anthropic 官方 marketplace**（PR #148 in `anthropics/claude-plugins-official`）
- **核心价值**：把 CC 从"智能补全"升级为"资深 AI 工程师"的方法论框架
- **特色 Skill**：
  - `brainstorming` — 写代码前的苏格拉底式追问
  - `writing-plans` — 任务拆成 2-5 分钟微任务
  - `subagent-driven-development` — 派发独立 subagent 处理 + 两阶段评审
  - `test-driven-development` — 强制 RED-GREEN-REFACTOR
  - `systematic-debugging` — 4 阶段根因分析
  - `using-git-worktrees` — 隔离工作空间
- **正确安装命令**（来源：官方 marketplace 仓库）：
  ```
  /plugin install superpowers@claude-plugins-official
  ```
- ⚠️ **作者立场**：Jesse Vincent 明确反对修改 Skill 内容以"合规"其他治理规范，因内容已过对抗式压力测试。**与凯戈现有的 CLAUDE.md / system-prompt / feedback memory 治理体系会争抢优先级**——装之前要明确取舍。

### 3.2 Tier A — 优质策展

| 仓库 | 价值 | 注意 |
|---|---|---|
| [VoltAgent/awesome-agent-skills](https://github.com/VoltAgent/awesome-agent-skills) | 1000+ skills，明确剔除 AI 批量生成；含 Anthropic / Google Labs / Vercel / Stripe / Cloudflare / Sentry / HuggingFace / Figma 等官方 skill | 跨多 Agent 工具兼容 |
| [travisvn/awesome-claude-skills](https://github.com/travisvn/awesome-claude-skills) | 经典 awesome 风格，按场景分类 | 入门导览首选 |
| [karanb192/awesome-claude-skills](https://github.com/karanb192/awesome-claude-skills) | 50+ verified，带 ⭐ 评级和验证状态 | 适合快速决策 |
| [ComposioHQ/awesome-claude-skills](https://github.com/ComposioHQ/awesome-claude-skills) | 含 Composio connect-apps，连 500+ 应用 | 商业背书 |
| [alirezarezvani/claude-skills](https://github.com/alirezarezvani/claude-skills) | 232+ skills，工程/营销/产品/合规/咨询；零依赖 Python | 跨 12 个 AI 工具 |
| [daymade/claude-code-skills](https://github.com/daymade/claude-code-skills) | 个人精品 marketplace，含 prompt-optimizer / ppt-creator / youtube-downloader | 中文友好，**架构值得借鉴** |
| [sickn33/antigravity-awesome-skills](https://github.com/sickn33/antigravity-awesome-skills) | 1400+ skills，含 npm 安装器 | 体量大，有筛选机制 |

### 3.3 Tier B — 垂直领域

- **科学计算**：K-Dense-AI/claude-scientific-skills
- **法律**：lawvable/awesome-legal-skills
- **安全**：BehiSecc/vibesec / prompt-security/clawsec
- **VMware/IT 运维**：zw008/VMware-AIops
- **Apple 生态**：more-io/apple-bridges（Reminders/Calendar/Notes/Mail/tmux 桥接）
- **Home Assistant**：komal-SkyNET/claude-skill-homeassistant
- **创意/媒体**：deapi-ai/claude-code-skills（图像/TTS/视频）
- **Node.js**：mcollina/skills（Matteo Collina，Fastify 作者）
- **Svelte**：spences10/svelte-skills-kit

### 3.4 三大聚合站

| 站点 | 内容 |
|---|---|
| [claudemarketplaces.com](https://claudemarketplaces.com/) | 4200+ skills, 770+ MCP, 2500+ marketplaces |
| [awesomeclaudeskills.com](https://awesomeclaudeskills.com/) | 按 stars 排序的精选目录 |
| [claudepluginhub.com](https://www.claudepluginhub.com/) | 详细 plugin 拆解 |

---

## 四、业界深度观点

### 4.1 Simon Willison — "bigger deal than MCP"

- **文章**：[Claude Skills are awesome, maybe a bigger deal than MCP](https://simonwillison.net/2025/Oct/16/claude-skills/)
- **日期**：2025-10-16（X 推文链接：[@simonw/1978935386496995811](https://x.com/simonw/status/1978935386496995811)）
- **核心论点**：
  - MCP 是完整协议规范（hosts/clients/servers/resources/prompts/tools/sampling/roots/elicitation + 三种传输）
  - Skills 是 Markdown + YAML metadata + 可选脚本——更接近 LLM 的本质
  - "Skills 把困难部分外包给 LLM 自身的能力"
  - LLM 已会用 `cli-tool --help`，所以 Skills 比 MCP 更省 token

### 4.2 Armin Ronacher（Flask 作者）

- 已完全从 MCP 迁移到 Skills
- 论点："Skills 本质上就是个简短摘要，告诉 Agent 有哪些能力以及在哪里找细节。Skills 不向上下文注入任何工具定义。工具还是同一个 bash 和 Agent 已有的工具。"

### 4.3 Jesse Vincent（obra/superpowers 作者）

- **关键文章**：[Superpowers: How I'm using coding agents in October 2025](https://blog.fsck.com/2025/10/09/superpowers/)
- **设计哲学**：
  - 强制流程：brainstorm → plan → implement
  - YAGNI（You Aren't Gonna Need It）
  - "Evidence over claims"——Claude 必须验证，不能说 "this should work"
  - 失败 3 次后强制架构评审
  - Subagent 派发 + 两阶段评审

### 4.4 Ruben Hassid（"How to AI" Substack）

- **关键洞见**：**"`Do NOT use for...` 比 `Use when...` 更重要"**——description 的边界定义比触发条件更关键
- **调试技巧**：问 Claude "你什么时候会使用这个 skill？"，它会把 description 原样背诵给你，立即看出哪里不对

### 4.5 ScaleKit / Smithery — CLI vs MCP 基准之争（修正版）

⚠️ **网络上常被混淆，实际是两份独立研究、结论不同**：

#### ScaleKit 一手数据（[scalekit-inc/mcp-vs-cli-benchmark](https://github.com/scalekit-inc/mcp-vs-cli-benchmark)）

- **测试规模**：**75 次**（25 样本 × 3 接口），单任务族（GitHub repo 查询）
- **结果**（同 prompt）：
  - CLI: **1,365 token**, 100% 可靠
  - CLI + Skills: **4,724 token**
  - MCP: **44,026 token**, 72% 可靠
- **结论**：CLI 比 MCP 省 **10–32 倍 token**
- **方法论局限**：仅 25 样本/组、单任务族，外推一般场景需谨慎

#### Smithery 独立研究（[smithery.ai/blog/mcp-vs-cli-is-the-wrong-fight](https://smithery.ai/blog/mcp-vs-cli-is-the-wrong-fight)）

- **测试规模**：**756 次**（3 API × 8 实验族）
- **结论方向相反**：MCP 91.7% vs CLI 83.3% 可靠性
- **Smithery 立场**：MCP 在多任务族下更可靠

#### 关键提醒
- 中文博客 trashwbin.top 把两份研究张冠李戴（ScaleKit 的 75 误为 Smithery 的 756）
- 引用任何一方数字时，**必须明示来源 + 任务范围**，否则误导
- 真实结论：**取决于任务复杂度**，无单一胜出方

---

## 五、Skills × MCP × CLI 三层架构

```
   ┌──────────────────────────────────────┐
   │         User Request                  │
   └──────────────────────────────────────┘
                     │
                     ▼
   ┌──────────────────────────────────────┐
   │   Skills（行为层 / 知识层）          │
   │   - 何时做、按什么顺序、用什么约束    │
   │   - Markdown 主导，~100 token 闲置    │
   └──────────────────────────────────────┘
                     │
        ┌────────────┴────────────┐
        ▼                          ▼
   ┌─────────────┐          ┌─────────────┐
   │ MCP（连接层）│          │ CLI（动作层）│
   │ 认证/传输    │          │ bash/git/gh │
   │ 工具发现     │          │ python/curl │
   └─────────────┘          └─────────────┘
```

**生产环境共识**：
- 三者协同，**不是择一**
- **Skill 写"怎么做"，CLI 执行"实际操作"，MCP 处理"复杂认证流"**
- 简单任务（git）优先 CLI，因 LLM 已熟悉
- 标准化 SaaS 接入用 MCP（Slack / Linear / Asana）
- 重复性流程封装为 Skill

---

## 六、避坑清单（生态特有）

### 6.1 安全

- ⚠️ **不安装来源不明的 Skill**——Skills 可执行任意代码
- ⚠️ **审计 SKILL.md 的网络请求和文件访问**——特别警惕外部 URL fetch
- ⚠️ **关注作者历史**——优先 Anthropic 官方、知名工程师（Jesse Vincent / Matteo Collina）
- ⚠️ **Agent Skills 不在 ZDR 范围内**——Skill 定义和执行数据按标准保留策略

### 6.2 工程

- 📌 **每个 Skill 启动占 ~100 token**——装太多稀释 CLAUDE.md 优先级
- 📌 **Skill 与 CLAUDE.md / 项目级指令会争夺优先级**——需取舍策略
- 📌 **plugin skills 有命名空间**——形如 `/my-plugin:command`
- 📌 **某些 marketplace 的 1000+ skill 大量为 AI 批量生成**——别被数字吓到，看核心精品
- 📌 **API 用 Skills 需开 Code Execution Tool beta**——三个 beta header

### 6.3 工作流

- 🎯 **同一任务输入超过 3 次相同 prompt**——立即封装为 Skill（社区共识）
- 🎯 **`Do NOT use for` 比 `Use when` 重要**——防止劫持（Ruben Hassid）
- 🎯 **不要让 Skill 假定自己是唯一被加载的**——多个 Skill 会同时存在
- 🎯 **Skill 不是 prompt**——是塑造 Agent 行为的代码
- 🎯 **不要复制别人未经评估的最佳实践**——obra 明确反对盲目"合规"

---

## 七、时间线（事实核对版）

- **2025-10-09** — Jesse Vincent 发布 Superpowers 设计哲学（[blog.fsck.com](https://blog.fsck.com/2025/10/09/superpowers/)）
- **2025-10-16** — 🎉 Anthropic 正式发布 Skills；Simon Willison 发文 "bigger deal than MCP"
- **2025-10-17 起** — 社区大量 dev.to / Medium 教程涌现
- **2025-12-18** — 🎉 Anthropic 发布 Agent Skills 为开放标准（agentskills.io）；发布日点名合作方：MS / OpenAI / Atlassian / Figma / Cursor / GitHub
- **2026-01-15** — Superpowers 进入 Anthropic 官方 marketplace（PR #148）
- **2026-03** — ScaleKit / Smithery 各自发布 CLI vs MCP 基准（结论不同）
- **2026-04** — "Delete your MCPs" 类博客在 HN 走红
- **2026-05** — 生态趋稳，多平台跨工具兼容

---

## 八、持续追踪渠道

| 渠道 | 性质 | 关注度 |
|---|---|---|
| [github.com/anthropics/skills](https://github.com/anthropics/skills) | 官方 | ⭐⭐⭐⭐⭐ |
| [github.com/anthropics/claude-plugins-official](https://github.com/anthropics/claude-plugins-official) | 官方 marketplace | ⭐⭐⭐⭐⭐ |
| [github.com/obra/superpowers](https://github.com/obra/superpowers) | 社区头牌 | ⭐⭐⭐⭐⭐ |
| [simonwillison.net/tags/skills/](https://simonwillison.net/tags/skills/) | Simon 博客标签 | ⭐⭐⭐⭐ |
| [blog.fsck.com](https://blog.fsck.com)（Jesse Vincent） | obra 个人博客 | ⭐⭐⭐⭐ |
| [github.com/topics/claude-skills](https://github.com/topics/claude-skills) | GitHub 话题页 | ⭐⭐⭐ |
| [claudemarketplaces.com](https://claudemarketplaces.com) | 聚合站 | ⭐⭐⭐ |

---

## 九、本 KB 文件互引关系

| 文件 | 定位 |
|---|---|
| [[skills]] | **Skills 实战指南** — 概念辨别 + 视频案例 + 项目矩阵 + 课件大纲 |
| [[skills-howto]] | **Skill 操作手册** — 机制/安装/Skill Creator/最小示例 |
| [[skills-factcheck-2026-05-05]] | **事实核对研究痕迹** — 网页版 KB 三大硬伤记录 |
| **本文 [[skills-ecosystem]]** | **生态地图** — 官方资源 / 社区项目 / 业界观点 / 避坑清单 |

---

## 十、来源（核对过的）

- [Anthropic — Equipping agents with Agent Skills](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)
- [agentskills.io](https://agentskills.io)
- [SiliconANGLE — Anthropic makes agent Skills an open standard (2025-12-18)](https://siliconangle.com/2025/12/18/anthropic-makes-agent-skills-open-standard/)
- [VentureBeat — Anthropic launches enterprise Agent Skills](https://venturebeat.com/ai/anthropic-launches-enterprise-agent-skills-and-opens-the-standard)
- [The New Stack — Agent Skills: Anthropic's Next Bid](https://thenewstack.io/agent-skills-anthropics-next-bid-to-define-ai-standards/)
- [Simon Willison — Claude Skills are awesome](https://simonwillison.net/2025/Oct/16/claude-skills/)
- [Jesse Vincent — Superpowers blog](https://blog.fsck.com/2025/10/09/superpowers/)
- [ScaleKit MCP vs CLI Benchmark](https://www.scalekit.com/blog/mcp-vs-cli-use)
- [scalekit-inc/mcp-vs-cli-benchmark](https://github.com/scalekit-inc/mcp-vs-cli-benchmark)
- [Smithery — MCP vs CLI Is the Wrong Fight](https://smithery.ai/blog/mcp-vs-cli-is-the-wrong-fight)
- [anthropics/claude-plugins-official](https://github.com/anthropics/claude-plugins-official)

> 已剔除：trashwbin.top（张冠李戴二手源）/ 项目细节幻觉段落（Day 4 / LEVEL3-GOVERNANCE 等）/ "发布日跨 7 CLI 同步采纳"误述
