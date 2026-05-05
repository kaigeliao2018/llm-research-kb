# Claude Skills 知识库

> **整理日期**：2026-05-05
> **整理人**：Claude（基于 Web/GitHub 深度搜索）
> **用途**：Magikid AIRobotics Lab 内部知识库 · 供 CCC 工作流参考
> **建议归档位置**：`/Users/kaigeliao/magikid-projects-hub/knowledge-assets/claude-skills-kb.md`

---

## 0. 一页速览（TL;DR）

**Skill 是什么**：一个文件夹，里面放一个 `SKILL.md`（YAML frontmatter + Markdown 指令），可选 scripts/、references/、assets/。Anthropic 2025 年 10 月 16 日发布，2025 年 12 月作为开放标准发布在 [agentskills.io](https://agentskills.io)。

**它解决的问题**：让 Agent 在需要时再加载相关知识（progressive disclosure），而不是在系统提示里塞满所有上下文。每个 Skill 启动时只占约 100 token（仅 metadata），触发后才加载完整 SKILL.md（通常 < 5000 token）。

**和 MCP 的关系**：互补，不互斥。MCP 负责连接（access），Skills 负责行为（behavior）。一个真实的对比基准（ScaleKit）：同一 GitHub 任务，CLI 用 1,365 token，CLI+Skills 用 4,724 token，MCP 用 44,026 token。

**跨平台支持**：Claude.ai / Claude Code / Claude API / Codex / Gemini CLI / Cursor / Antigravity / OpenCode / Windsurf 都已支持。

**安全警告**：Anthropic 官方建议「只使用来自可信来源的 Skills」。Skills 可执行任意代码，恶意 Skill 可导致数据泄漏。

---

## 1. 官方权威资源（最高优先级，必读）

### 1.1 Anthropic 官方

| 资源 | 链接 | 用途 |
|---|---|---|
| 官方仓库 | https://github.com/anthropics/skills | 17 个官方 Skill 源码（127k+ stars） |
| 官方公告（工程博客） | https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills | 设计原理、progressive disclosure 完整解释 |
| Agent Skills API 文档 | https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview | API 调用规范 |
| Claude Code Skills 文档 | https://code.claude.com/docs/en/skills | CC 中创建/管理 Skill 的完整指南 |
| 开放标准官网 | https://agentskills.io | 跨平台 Skill 标准定义 |
| 创建 Skill 完整指南（PDF） | https://resources.anthropic.com/hubfs/The-Complete-Guide-to-Building-Skill-for-Claude.pdf | 28 页详细文档 |
| Skills Cookbook | 官方 Skills 仓库内 | 含完整代码示例 |
| 用户帮助中心 | https://support.claude.com/en/articles/12512198-how-to-create-custom-skills | 非工程视角的入门 |

### 1.2 Anthropic 官方 17 个 Skill 全景

**文档处理类（Source-available）：**
- `docx` - Word 文档创建/编辑/批注/修订追踪
- `pdf` - 文本提取、表格、合并、注释
- `pptx` - 演示文稿生成
- `xlsx` - 电子表格、公式、图表

**创意设计类（开源 Apache 2.0）：**
- `algorithmic-art` - p5.js 生成式艺术
- `canvas-design` - PNG/PDF 视觉艺术设计
- `slack-gif-creator` - Slack 优化的 GIF
- `artifacts-builder` - React + Tailwind + shadcn/ui 复杂 artifacts
- `frontend-design` - 高质量前端 UI（避免 AI slop）
- `anthropic-branding` - 应用 Anthropic 官方品牌

**开发技术类：**
- `claude-api` - Claude API 完整参考材料（8 种语言）
- `mcp-builder` - 创建高质量 MCP 服务器
- `skill-creator` - 元 Skill：创建新 Skill
- `webapp-testing` - Playwright 自动化测试

**企业沟通类：**
- `doc-coauthoring` - 协作文档创作
- 其他企业沟通类 Skill

📍 **完整中文解读**：https://claude-world.com/articles/anthropic-official-skills-complete-guide/

---

## 2. 高质量社区资源（精选，已交叉验证）

### 2.1 Tier S - 头牌项目（务必关注）

#### obra/superpowers
- **链接**：https://github.com/obra/superpowers
- **作者**：Jesse Vincent（Prime Radiant）
- **Stars**：177.6k+（截至 2026-04，过去 30 天涨 41.6k）
- **状态**：已收录进 Anthropic 官方 marketplace（2026-01-15）
- **核心价值**：把 Claude Code 从「智能补全」升级为「资深 AI 工程师」的方法论框架
- **特色 Skill**：
  - `brainstorming` - 写代码前的苏格拉底式追问
  - `writing-plans` - 把任务拆成 2-5 分钟的微任务
  - `subagent-driven-development` - 派发独立 subagent 处理任务，两阶段评审
  - `test-driven-development` - 强制 RED-GREEN-REFACTOR
  - `systematic-debugging` - 4 阶段根因分析
  - `using-git-worktrees` - 隔离工作空间
- **安装**：
  ```bash
  /plugin marketplace add obra/superpowers-marketplace
  /plugin install superpowers@superpowers-marketplace
  ```
- **重要警告**：作者明确反对修改 Skill 内容以「符合」Anthropic 文档，因其内容已过对抗式压力测试。其哲学和你现有的 LEVEL3-GOVERNANCE-FRAMEWORK 在某些点会冲突，需慎重评估。

#### anthropics/skills
- 官方 17 Skill 仓库，见上节

#### K-Dense-AI/claude-scientific-skills
- **链接**：在 GitHub topic `claude-skills` 下可搜到
- **价值**：科研、生物信息、化学信息、材料科学的 Skill 集合
- **对你的相关性**：可作为 magikid-ai-maker-projects 课程内容研发的灵感源

### 2.2 Tier A - 优质策展（按价值排序）

| 仓库 | 价值 | 注意 |
|---|---|---|
| [VoltAgent/awesome-agent-skills](https://github.com/VoltAgent/awesome-agent-skills) | 1000+ skills，明确剔除 AI 批量生成内容；含 Anthropic、Google Labs、Vercel、Stripe、Cloudflare、Sentry、Hugging Face、Figma 等官方团队的 skill | 跨多 Agent 工具兼容 |
| [travisvn/awesome-claude-skills](https://github.com/travisvn/awesome-claude-skills) | 经典 awesome 风格目录，按场景分类 | 入门导览首选 |
| [karanb192/awesome-claude-skills](https://github.com/karanb192/awesome-claude-skills) | 50+ verified skills，带 ⭐ 评级和验证状态 | 适合快速决策 |
| [ComposioHQ/awesome-claude-skills](https://github.com/ComposioHQ/awesome-claude-skills) | 含 Composio 的 connect-apps 插件，连接 500+ 应用 | 商业背书强 |
| [sickn33/antigravity-awesome-skills](https://github.com/sickn33/antigravity-awesome-skills) | 1400+ skills，含 npm 安装器，跨平台兼容 | 体量大但有筛选机制 |
| [BehiSecc/awesome-claude-skills](https://github.com/BehiSecc/awesome-claude-skills) | 涵盖 TTS、视频、音乐、健康、DNA 分析等垂直应用 | 创意类丰富 |
| [JayZeeDesign/awesome-claude-skills](https://github.com/JayZeeDesign/awesome-claude-skills) | 教学类示例 | 适合学习参考 |
| [alirezarezvani/claude-skills](https://github.com/alirezarezvani/claude-skills) | 232+ skills，覆盖工程、营销、产品、合规、C 级管理咨询；零依赖 Python | 5200+ stars，跨 12 个 AI 工具 |
| [daymade/claude-code-skills](https://github.com/daymade/claude-code-skills) | 个人精品 marketplace，含 prompt-optimizer、claude-code-history-files-finder、ppt-creator、youtube-downloader 等实用 skill | 中文友好，值得借鉴架构 |

### 2.3 Tier B - 垂直领域（按需查阅）

- **科学计算**：K-Dense-AI/claude-scientific-skills
- **法律**：lawvable/awesome-legal-skills
- **安全**：BehiSecc/vibesec、prompt-security/clawsec
- **VMware/IT 运维**：zw008/VMware-AIops
- **Apple 生态**：more-io/apple-bridges（Reminders、Calendar、Notes、Mail、tmux 桥接）
- **Home Assistant**：komal-SkyNET/claude-skill-homeassistant
- **创意/媒体**：deapi-ai/claude-code-skills（图像、TTS、视频生成）
- **Node.js 生态**：mcollina/skills（Matteo Collina，Fastify 作者）
- **Svelte**：spences10/svelte-skills-kit

### 2.4 marketplace 聚合站

- [claudemarketplaces.com](https://claudemarketplaces.com/) - 4200+ skills, 770+ MCP, 2500+ marketplaces
- [awesomeclaudeskills.com](https://awesomeclaudeskills.com/) - 按 stars 排序的精选目录
- [claudepluginhub.com](https://www.claudepluginhub.com/) - 详细的 plugin 拆解

---

## 3. 业界深度观点（必读高质量分析）

### 3.1 Simon Willison（Python 社区领袖，Datasette 作者）

- **核心文章**：[Claude Skills are awesome, maybe a bigger deal than MCP](https://simonwillison.net/2025/Oct/16/claude-skills/)（2025-10-16，X 上 422k 浏览）
- **核心论点**：
  - MCP 是完整协议规范（hosts/clients/servers/resources/prompts/tools/sampling/roots/elicitation + 三种传输）
  - Skills 是 Markdown + 一点 YAML metadata + 可选脚本——更接近 LLM 的本质
  - "Skills 把困难部分外包给 LLM 自身的能力"
  - LLM 已经会用 `cli-tool --help`，所以 Skills 比 MCP 更省 token
- **后续讨论**：HN 顶帖（item id 45619537），Lobsters 讨论激烈

### 3.2 Armin Ronacher（Flask 作者）

- 已完全从 MCP 迁移到 Skills
- 核心论点："Skills 本质上就是个简短摘要，告诉 Agent 有哪些能力以及在哪里找细节。Skills 不向上下文注入任何工具定义。工具还是同一个 bash 和 Agent 已有的工具。"

### 3.3 Smithery / ScaleKit 基准测试

- **测试规模**：756 次 benchmark（Codex 和 Claude Code）
- **关键数据**（GitHub 任务，相同 prompt）：
  - CLI: 1,365 token，可靠性 100%
  - CLI + Skills: 4,724 token
  - MCP: 44,026 token，可靠性 72%
- **结论**：CLI 比 MCP 便宜 17 倍，可靠性更高
- **完整分析**：https://blog.trashwbin.top/en/posts/cli-vs-mcp-vs-skills/

### 3.4 Jesse Vincent（obra/superpowers 作者）

- **关键文章**：[Superpowers: How I'm using coding agents in October 2025](https://blog.fsck.com/2025/10/09/superpowers/)
- **设计哲学**：
  - 强制流程：brainstorm → plan → implement
  - YAGNI（You Aren't Gonna Need It）原则
  - "Evidence over claims"——Claude 必须验证，不能说 "this should work"
  - 失败 3 次后强制架构评审
  - Subagent 派发 + 两阶段评审

### 3.5 Ruben Hassid（"How to AI" Substack，420k+ 读者）

- **关键文章**：[Claude Skills](https://ruben.substack.com/p/claude-skills)
- **关键洞见**："`Do NOT use for...` 比 `Use when...` 更重要"——Skill description 的边界定义比触发条件更关键
- **调试技巧**："问 Claude：'你什么时候会使用这个 skill？'，它会把 description 原样背诵给你，立即看出哪里不对"

### 3.6 Nick Babich（UX Planet）

- 完整中文友好的非编程人士教程
- https://uxplanet.org/complete-guide-to-creating-your-own-claude-skill-44873d1f49ee

---

## 4. YouTube 视频资源（精选）

> 注：YouTube 上 Skills 相关视频质量参差，多数是带货课程。下面只列经过验证的合理视频，标注内容深度。

| 视频 | 频道 | 价值 | 链接 |
|---|---|---|---|
| Claude Skills Explained - Step-by-Step Tutorial for Beginners | 综合频道 | 入门基础 | https://www.youtube.com/watch?v=wO8EboopboU |
| Full Claude Skills Tutorial for Beginners in 2026! (Become a PRO) | AI Foundations | 中级实操 | https://www.youtube.com/watch?v=YkpEX_jlb04 |
| Claude AI Full Tutorial: From Basics to Agentic AI (2026) | 综合 | 完整生态导览 | https://www.youtube.com/watch?v=XTWb5oEfqdY |
| FULL Claude Tutorial For Beginners in 2026! (FULL COURSE) | AI Foundations | 系统课 | https://www.youtube.com/watch?v=Xg55nTrbYYY |

**Medium 上有人做了 25 个视频排名分析**（更省时间）：
https://medium.com/@rentierdigital/i-watched-25-claude-code-youtube-videos-so-you-dont-have-to-the-definitive-ranking-550aa6863840

---

## 5. SKILL.md 标准格式参考

### 5.1 最小可用模板

```markdown
---
name: my-skill-name
description: A clear description of what this skill does and when to use it. Use when... Do NOT use for...
---

# My Skill Name

## What This Does
[一句话描述]

## When to Use This Skill
- 用例 1
- 用例 2

## Instructions
[Claude 应遵循的步骤]

## Examples
[输入输出示例]
```

### 5.2 进阶目录结构

```
my-skill/
├── SKILL.md              # 必需：指令 + frontmatter
├── references/           # 可选：参考文档（按需加载）
│   └── REFERENCE.md
├── scripts/              # 可选：辅助脚本
│   └── helper.py
├── assets/               # 可选：模板、配置
└── evals/                # 推荐：评估测试
    └── evals.json
```

### 5.3 关键最佳实践（来自官方 + 社区共识）

1. **SKILL.md 控制在 500 行以内** - 减少 context 膨胀
2. **description 字段是核心** - 它决定 Skill 是否被触发，必须包含「何时用」和「何时不用」
3. **写给另一个 Claude 看，不是写给人看** - 包含对 Claude 有用且非显然的信息
4. **优先具体示例而非抽象指令**
5. **细节内容拆到 references/ 或 scripts/** - 显式引用让 Claude 知道何时读
6. **用 evals 做对照测试** - with-skill vs baseline 平行对比
7. **逐步迭代** - 每次重大修改后立即测试

---

## 6. 安装/管理速查（Claude Code）

### 6.1 安装官方 Skills

```bash
# 添加 Anthropic 官方 marketplace
/plugin marketplace add anthropics/skills

# 安装文档处理 Skills
/plugin install document-skills@anthropic-agent-skills

# 安装示例 Skills
/plugin install example-skills@anthropic-agent-skills
```

### 6.2 添加第三方 marketplace

```bash
# Superpowers
/plugin marketplace add obra/superpowers-marketplace
/plugin install superpowers@superpowers-marketplace

# alirezarezvani 全套
/plugin marketplace add alirezarezvani/claude-skills
/plugin install engineering-skills@claude-code-skills

# 自有 marketplace
/plugin marketplace add your-org/your-marketplace
```

### 6.3 个人 Skill 目录

- 全局：`~/.claude/skills/`
- 项目：`.claude/skills/`
- monorepo 子包：`packages/<name>/.claude/skills/`

### 6.4 调试与重载

```bash
/reload-plugins                              # 重载所有插件
rm -rf ~/.claude/plugins/cache               # 清缓存（修复加载问题）
```

### 6.5 写完 Skill 自检

直接问 Claude：「你什么时候会使用 my-skill-name 这个 skill？」
如果回答模糊或方向不对，**改 description 字段**，不是改正文。

---

## 7. Skills × 你的项目矩阵

### 7.1 强匹配组合（建议优先评估）

| 项目 | 推荐 Skill 类型 | 来源 |
|---|---|---|
| **VEX-IQ-KB** | `pdf` (官方)、`skill-creator`（封装 ingest pipeline）、tmux 类工具 skill | anthropics/skills |
| **vex-iq-build-assistant** | `frontend-design`、`mcp-builder`（如 Phase 2 暴露接口给外部）、`claude-api` | anthropics/skills |
| **vex-iq-parts-lib** | 自建结构化资产管理 skill（参考 daymade/claude-code-skills 架构） | 自研 |
| **pegatron-rma-project** | `webapp-testing`（仪表盘验证）、`docx`（双语报告）、自建 RMA SOP skill | anthropics/skills |
| **magikid-projects-hub** | `skill-creator`、自建治理 skill（封装 LEVEL3-GOVERNANCE 框架） | 自研为主 |
| **learning-research-hub** | K-Dense-AI 科研 skills、自建研究流程 skill | 社区 + 自研 |

### 7.2 弱匹配 / 不推荐（避免噪音）

| Skill | 原因 |
|---|---|
| obra/superpowers 全套 | 与你已有的 LEVEL3-GOVERNANCE-FRAMEWORK 强重叠，会争抢优先级 |
| Marketing Skills 大包 | 你是技术 CTO 路线，可由其他岗位独立处理 |
| GPT Researcher 类 | 与你 fork 的 Karpathy AutoResearch 体系强重叠 |
| 大量低星 AI 批量生成 skill | 噪音多，安全风险高 |

### 7.3 建议的 Skill 自研清单（基于你现有工作流）

按 ROI 排序：

1. **vex-iq-pdf-ingest** - 封装 Day 1-2 的 PDF→MD 流程（pdfplumber + PyMuPDF + 结构化输出）
2. **magikid-three-stage-workflow** - 封装"discuss in Project chat → iterate in Artifacts → execute via CCC"
3. **ldcad-parts-lib-l3-l4-promotion** - 封装五层 parts 库的 L3→L4 晋级流程
4. **vex-competition-tracker-deploy** - 封装 worlds.html 的双语生成与部署流程
5. **pegatron-rma-bilingual-report** - 封装 docx 双语阶段报告输出

---

## 8. 避坑清单（生态特有的陷阱）

### 8.1 安全相关

- ⚠️ **不安装来源不明的 Skill** - Skills 可执行任意代码
- ⚠️ **审计 SKILL.md 中的网络请求和文件访问** - 特别警惕外部 URL fetch
- ⚠️ **关注作者历史** - 优先 Anthropic 官方、知名工程师（Jesse Vincent、Matteo Collina 等）
- ⚠️ **Agent Skills 不在 ZDR 范围内** - Skill 定义和执行数据按标准数据保留策略保留

### 8.2 工程相关

- 📌 **每个 Skill 启动时占 ~100 token** - 装太多会稀释 CLAUDE.md 优先级
- 📌 **Skill 与 CLAUDE.md / 项目级指令会争夺优先级** - 需有取舍策略
- 📌 **plugin skills 有命名空间** - 形如 `/my-plugin:command` 而非 `/command`
- 📌 **某些 marketplace 的 1000+ skill 标榜大量为 AI 批量生成** - 别被数字吓到，只看核心精品
- 📌 **Linux 路径 bug**（已知）- Agent SDK 在某些版本硬编码了 macOS 路径
- 📌 **API 用 Skills 需开 Code Execution Tool beta** - 三个 beta header

### 8.3 工作流相关

- 🎯 **同一任务输入超过 3 次相同 prompt** - 立即封装为 Skill（社区共识）
- 🎯 **Skill description 中"Do NOT use for"比"Use when"重要** - 防止劫持
- 🎯 **不要让 Skill 假定自己是唯一被加载的** - 多个 Skill 会同时存在
- 🎯 **Skill 不是 prompt** - 是塑造 Agent 行为的代码
- 🎯 **不要复制别人未经评估的最佳实践** - obra 明确反对盲目"合规"

---

## 9. Skills × MCP × CLI 三者关系（生态最重要的认知）

```
   ┌──────────────────────────────────────┐
   │         User Request                  │
   └──────────────────────────────────────┘
                     │
                     ▼
   ┌──────────────────────────────────────┐
   │   Skills（行为层 / 知识层）          │
   │   - 何时做、按什么顺序、用什么约束    │
   │   - Markdown 主导，~30 token 闲置     │
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

**生产环境的最佳实践**（共识）：

- 三者协同，不是择一
- **Skill 写"怎么做"，CLI 执行"实际操作"，MCP 处理"复杂认证流"**
- 简单任务（如 git）优先 CLI，因 LLM 已经熟悉
- 标准化 SaaS 接入用 MCP（Slack、Linear、Asana 等）
- 重复性流程封装为 Skill

---

## 10. 持续追踪渠道

| 渠道 | 性质 | 关注度 |
|---|---|---|
| https://github.com/anthropics/skills | 官方 | ⭐⭐⭐⭐⭐ |
| https://github.com/obra/superpowers | 社区头牌 | ⭐⭐⭐⭐⭐ |
| https://simonwillison.net/tags/skills/ | Simon Willison 博客标签 | ⭐⭐⭐⭐ |
| https://blog.fsck.com（Jesse Vincent） | obra 个人博客 | ⭐⭐⭐⭐ |
| https://github.com/topics/claude-skills | GitHub 话题页 | ⭐⭐⭐ |
| https://claudemarketplaces.com | 聚合站 | ⭐⭐⭐ |
| https://news.ycombinator.com（搜 Claude Skills） | 社区讨论 | ⭐⭐⭐ |
| https://releasebot.io/updates/anthropic | Anthropic release notes | ⭐⭐⭐ |

---

## 11. 时间线（关键事件）

- **2025-10-09** - Jesse Vincent 发布 Superpowers 设计哲学
- **2025-10-16** - 🎉 Anthropic 正式发布 Skills；Simon Willison 发文"bigger deal than MCP"
- **2025-10-17** - 社区开始大量发布 dev.to/Medium 教程
- **2025-10-18** - obra/superpowers 等头部社区仓库涌现
- **2025-11-13** - Anthropic 发布"Skills Explained"完整指南
- **2025-12-18** - Anthropic 发布 Agent Skills 为开放标准（agentskills.io）
- **2026-01-15** - Superpowers 进入 Anthropic 官方 marketplace
- **2026-03** - CLI vs MCP vs Skills 大讨论；ScaleKit 公开基准测试
- **2026-03** - Smithery 发布 756 次基准对比，量化 token 消耗差异
- **2026-04** - 「Delete your MCPs」类博客在 HN 走红
- **2026-05** - 生态趋于稳定，多平台跨工具兼容

---

## 12. 给 Magikid 的下一步建议（落地路径）

1. **本周**：把这份 KB 入库到 `magikid-projects-hub/knowledge-assets/`
2. **本周**：为 VEX-IQ-KB Day 4 评估是否引入 `pdf` 官方 Skill 替代当前自建的 pdfplumber+PyMuPDF
3. **下周**：用 `skill-creator` 试做第一个自有 Skill：**vex-iq-pdf-ingest**
4. **2 周内**：评估是否将 LEVEL3-GOVERNANCE 框架的核心规则封装为一个治理 Skill
5. **1 个月内**：建立 magikid-skills marketplace（私有）作为内部 Skill 分发中心
6. **持续**：每月梳理 awesomeclaudeskills.com 头部新增 Skill，更新本 KB

---

## 附录 A：本 KB 编纂数据来源

- 6 轮 Web Search（涵盖 GitHub、Anthropic 官方文档、Simon Willison 博客、obra 博客、YouTube、生态分析文章）
- 60+ 条独立来源
- 交叉验证关键数据（如 Superpowers stars 数从多源核实）
- 时间戳：2026-05-05

## 附录 B：本 KB 未覆盖的内容（待后续补充）

- 中文社区 Skills 资源（搜索结果以英文为主）
- 教育垂直领域（K-12、STEM）专用 Skills
- VEX/LEGO/Robotics 领域是否已有相关 Skills（搜索未直接命中）
- 企业级 Skills 治理实践案例

---

*本知识库由 Claude（Opus 4.7）基于 web search 与 GitHub 检索结果编纂；引用数据基于公开来源，部分 stars 数随时间变动。建议每季度更新一次。*
