# Claude Code Skills 实战指南

> 研究日期：2026-05-05（v2，官方源核对版）
> 状态：方法论沉淀 + 完整清单 + 课件素材
> 研究员：凯戈 + Claude（Opus 4，三 agent 并行核对）
> 双重定位：知识库词条 + 课程课件素材源
> 相关：[[skills-howto]]（操作手册：机制/安装/最小示例）

---

## 一、概念澄清：Skill / MCP / Plugin / CLI 四类辨别

Claude Code 生态里"Skill"被泛化使用，**4 类东西经常被混为一谈**。装错了不仅没用，还会污染上下文窗口、稀释 CLAUDE.md 优先级。

| 类型 | 本质 | 安装方式 | 启动开销 | 例子 |
|------|------|----------|---------|------|
| **Anthropic 官方 Skill** | `SKILL.md` 文件机制，**渐进式加载**（启动只注入 frontmatter ~100 tokens/Skill） | `/plugin marketplace add anthropics/skills` + `/plugin install ...` | 极低 | pdf、docx、xlsx、frontend-design、skill-creator（共 17 个） |
| **MCP Server** | Model Context Protocol，独立进程提供工具 | `settings.json` 配置 + 启动服务 | 中（注入工具 schema） | Context7、GitHub MCP、Cloudflare MCP、Playwright MCP |
| **社区 Plugin / Prompt 包** | 第三方 prompt 集合，本质是规范注入 | git clone + 引用 / 复制到 CLAUDE.md | 高（常驻） | Superpowers、Systematic Debugging |
| **外部 CLI 工具** | 操作系统级工具，与 Claude 无关 | `brew install` / `apt install` | 零 | tmux、jq、ripgrep |

**辨别口诀**：
- 看到 `SKILL.md` → 真 Skill
- 看到 `mcpServers` 配置 → MCP（不是 Skill）
- 看到只是 markdown 规范 → Plugin
- 看到 `brew install` → CLI 工具

**关键事实**（[官方 README](https://github.com/anthropics/skills)）：
> "Skills are folders of instructions, scripts, and resources that Claude **loads dynamically** to improve performance on specialized tasks."

渐进式加载意味着 17 个 Skill 全装也只占 ~1.5k tokens 启动开销，命中才读正文。这是关键背景——**官方 Skill 远比 Plugin/MCP 节省上下文**。

---

## 二、Anthropic 官方 Skills 完整清单（17 个，[anthropics/skills](https://github.com/anthropics/skills) 128k★，2026-05-03 更新）

按用途分类：

### 文档处理（4 个）
| Skill | 功能 | 凯戈适配 |
|---|---|---|
| **pdf** | 读取/抽取/合并/拆分/水印/填表/OCR | 🟢 vex-iq-kb（Game Manual）/ pegatron（合同协议）/ llm-research-kb（白皮书） |
| **docx** | Word 创建/编辑/TOC/页眉/修订追踪 | 🟢 pegatron 客户交付 / ai-teaching-kb 课程报告 |
| **pptx** | PowerPoint 创建/编辑 | 🟡 vex 教学课件 / pegatron 演示 |
| **xlsx** | Excel/CSV/TSV 处理、公式、图表、清洗 | 🟢 RobotEvents API 数据分析 / vex 战情统计 |

### 前端 / 设计（6 个）
| Skill | 功能 | 凯戈适配 |
|---|---|---|
| **frontend-design** | 生产级 UI，规避 "AI slop" 美学 | 🟢 vex-iq-kb v3.0 / vexiq-ai-coder UI 升级 |
| **web-artifacts-builder** | React + Tailwind + shadcn/ui 复杂 artifact | 🟡 临时演示页 / 客户原型 |
| **canvas-design** | 海报/静态视觉设计 → .pdf/.png | 🟡 vex 比赛宣传海报 / 课程封面 |
| **theme-factory** | 10 种预设主题套用到 artifact | ⚪ 低频 |
| **brand-guidelines** | Anthropic 品牌色与字体应用 | ⚪ 仅 Anthropic 内部场景 |
| **algorithmic-art** | p5.js 生成艺术 | ⚪ 创意/教学示例 |

### 测试 / 开发（3 个）
| Skill | 功能 | 凯戈适配 |
|---|---|---|
| **webapp-testing** | **基于 Playwright** 自动测试本地 webapp，截图/日志 | 🟢 vex-iq-kb.pages.dev 回归测试 / pegatron 视觉自动化 |
| **claude-api** | 用 Anthropic SDK 构建/调试/迁移 Claude 应用 | 🟢 vexiq-ai-coder（Groq Whisper / 后续 Anthropic 接入） |
| **mcp-builder** | 用 FastMCP / TS SDK 构建 MCP server | 🟡 未来需要时再用 |

### 写作 / 协作（2 个）
| Skill | 功能 | 凯戈适配 |
|---|---|---|
| **doc-coauthoring** | 三阶段结构化文档共写工作流 | 🟢 教学课程内容 / 客户报告 |
| **internal-comms** | 3P updates / 简报 / FAQ 内部沟通 | ⚪ 个人开发者较少用 |

### 元工具 / 创意（2 个）
| Skill | 功能 | 凯戈适配 |
|---|---|---|
| **skill-creator** | 创建/优化/评估 Skill | 🟢 你已用过（自建 battle-report） |
| **slack-gif-creator** | Slack 优化的动图工具 | ⚪ 不用 Slack 不需要 |

**绿灯小计**：直接对凯戈现项目高匹配的官方 Skill = **9 个**（pdf / docx / pptx / xlsx / frontend-design / webapp-testing / claude-api / doc-coauthoring / skill-creator）

> 注：YouTube 视频里"agent-browser / Superpowers / Tavily / Context7 / tmux"——**官方仓库一律没有**，归社区/MCP/CLI。

---

## 三、核心 MCP Servers（不是 Skill，但配套讲）

| MCP | 仓库 | 维护方 | 凯戈适配 | 评分 |
|---|---|---|---|---|
| **GitHub MCP** | [github/github-mcp-server](https://github.com/github/github-mcp-server) | **GitHub 官方**（Go，Docker，OAuth） | 8+ 仓库 PR/Action/Issue 跨仓库管理 | 🟢 9 |
| **Context7** | [upstash/context7](https://github.com/upstash/context7) | Upstash 商业（社区主流） | Three.js / Electron / WebSerial / Recharts API 防幻觉 | 🟢 9 |
| **Cloudflare MCP** | [cloudflare/mcp-server-cloudflare](https://github.com/cloudflare/mcp-server-cloudflare) | **Cloudflare 官方**（Remote 托管 OAuth） | vex-iq-kb.pages.dev 部署/DNS/Workers | 🟢 8 |
| **Playwright MCP** | [microsoft/playwright-mcp](https://github.com/microsoft/playwright-mcp) | **Microsoft 官方** | pegatron 视觉自动化 / KB 站点验收 | 🟡 8 |
| **Tavily MCP** | tavily-ai/tavily-mcp | Tavily 官方 | RobotEvents 周边搜索 | 🟡 6 |

**避坑**：
- `Puppeteer MCP` 已 archived，迁 Playwright
- `Filesystem` / `Memory` / `Sequential Thinking` MCP：Claude Code 已内置等价能力，装上反而占 token
- `awesome-mcp` 列表里的小众包 90% 是 wrapper，认仓库 star + 维护方再装

---

## 四、案例研究：8 分钟视频"15 个顶级 Skills"评估（2026-05）

### 4.1 真伪辨别（用第一节口诀拆 + 官方仓库对账）

| 视频归类为 Skill | 实际类型 | 备注 |
|---|---|---|
| PDF Processing | ✅ 官方 Skill `pdf` | |
| DOCX | ✅ 官方 Skill `docx` | |
| Frontend Design | ✅ 官方 Skill `frontend-design` | |
| Skill Creator | ✅ 官方 Skill `skill-creator` | |
| **Webapp Testing** | ✅ **官方 Skill** `webapp-testing`（基于 Playwright）| ⚠️ 此前 v1 误判为社区，已修正 |
| Context7 | ❌ MCP Server | upstash 社区高质量 |
| Tavily | ❌ MCP Server | |
| tmux | ❌ CLI 工具 | 完全不是 Claude 概念 |
| GPT Researcher | ❌ 独立开源项目 | |
| agent-browser | ⚠️ 社区项目 | 官方仓库无对应 |
| Superpowers | ⚠️ 社区 Prompt 包 | "96k stars" 无法核实 |
| Systematic Debugging | ⚠️ 社区 prompt | 概念性内容 |
| Code Refactoring | ⚠️ 社区 prompt | |
| Marketing Skills | ⚠️ 社区包 | |
| Changelog Maintenance | ⚠️ 社区 | |

**结论**：视频里**真正的官方 Skill 有 5 个**（v1 误判 webapp-testing 为社区，本版修正）。

### 4.2 视频漏掉的官方好东西（重要）

视频提了 15 个，但 Anthropic 官方 17 个 Skill 中**漏报 12 个**——其中**对凯戈最有价值的漏报**：

- ⚠️ **xlsx**（Excel/CSV）—— RobotEvents 赛季数据分析直接受益
- ⚠️ **claude-api** —— vexiq-ai-coder 后续接入 Anthropic SDK 时用
- ⚠️ **doc-coauthoring** —— 教学/客户文档协作工作流
- ⚠️ **mcp-builder** —— 未来自建 MCP 时用
- ⚠️ **canvas-design / pptx** —— 课件设计 / 演示

**教训**：YouTube 视频选的是"流量友好"的 Skills，**不等于全集**。看博主总结之外，**永远去仓库源核对**。

### 4.3 网页端 Claude 分析的硬伤（保留教学价值）

让网页端 Claude 结合"凯戈现有项目"给建议时，它**编造了大量不存在的项目细节**：

| 网页端说法 | 实际情况（INDEX 对账） |
|---|---|
| "VEX-IQ-KB Day 4 / MinerU 一直失败" | vex-iq-kb 没有 Day 几迭代；MinerU 在 karpathy-kb-research 评估过且**没选用**（选了 fitz）|
| "LEVEL3-GOVERNANCE-FRAMEWORK.md + 4 份战略文档" | 不存在 |
| "Phase 2 四 Agent (Analyzer/Packer/Designer/Integrator)" | 虚构架构 |
| "magikid-projects-hub 集中管理 6 项目" | hub 是"按需"，活跃项目 8+ |

**教训（必须沉淀）**：
> LLM 拿到片段信息后，会**幻觉化项目细节**让建议看起来"贴身"。
> 必须拿真实 INDEX 对账，否则建议建立在沙堆上。

---

## 五、凯戈当前已有的 Skills（系统检测，2026-05-05）

```
当前会话已加载：
├── update-config         — 配置 settings.json
├── keybindings-help      — 键盘快捷键
├── simplify              — 代码审查 / 简化
├── loop                  — 定时循环任务
├── schedule              — 远程定时 agent
├── claude-api            — Claude API / SDK 应用 ✅ 官方 Skill 已装
├── battle-report         — VEX 世锦赛战情报告（凯戈自建）
├── claude-hud:setup      — statusline 配置
└── claude-hud:configure  — HUD 显示选项
```

**关键认知**：
- `battle-report` 是凯戈自建项目专属 Skill —— **Skill Creator 你已经用过**
- `claude-api` 已装在系统，是 17 个官方 Skill 之一

---

## 六、Skills × 凯戈项目实施路径（v2 三阶段）

按你三个目标（现有项目试用 / 未来项目储备 / 知识库+教学）重排：

### 阶段 A：现有项目立即试用（本周）

| # | 动作 | 落地项目 | 验证产出 |
|---|---|---|---|
| 1 | 装 `anthropics/skills` 整个 marketplace | 全局（一次到位 17 个） | `/plugin marketplace list` 确认 |
| 2 | 启用 `pdf` Skill | vex-iq-kb 解析 Game Manual v0.1 | 对比 fitz 产出（图像/表格/章节切分） |
| 3 | 启用 `xlsx` Skill | vex-competition-tracker 历史数据 | RobotEvents API JSON → xlsx 报表 |
| 4 | 装 **GitHub MCP** | 全局 | 跨 8+ 仓库批量 PR/Action 状态查询 |
| 5 | 装 **Context7 MCP** | vex-iq-build-assistant feature/analyzer | Three.js 调用对比记忆 vs 实时文档 |

### 阶段 B：未来项目储备（按触发条件）

| Skill / MCP | 触发场景 |
|---|---|
| `webapp-testing` | vex-iq-kb.pages.dev 上 v3.0 时回归测试 |
| `frontend-design` | vex-iq-kb v3.0 / vexiq-ai-coder UI 改版 |
| `docx` + `doc-coauthoring` | pegatron 进入交付期 / 季度报告 |
| `pptx` + `canvas-design` | 教学课件 / 客户演示 |
| **Cloudflare MCP** | vex-iq-kb 部署调整 / DNS 改动 |
| **Playwright MCP** | pegatron 视觉自动化 PoC |
| `mcp-builder` | 当某项目反复需要外部 API 时（如 RobotEvents 自建 MCP）|

### 阶段 C：沉淀为知识库 + 教学（持续）

| 产出 | 内容 |
|---|---|
| `skills.md`（本文件） | 概念 + 视频案例 + 项目矩阵（持续更新评分） |
| `skills-howto.md`（新增） | 操作手册：安装/机制/Skill Creator/最小示例 |
| 学生课件 | 三课时拆分（见 §8）|
| 个人自建 Skill 增长 | `battle-report` 之后的下一个：候选 `kb-ingestor`（PDF→wiki 流程封装） |

---

## 七、不装清单（理由比"不推荐"重要）

| Skill / 工具 | 不装的真正原因 |
|---|---|
| Superpowers | 已有 `system-prompt.md` + `coding-standards.md` + 8+ 条 feedback memory，再注入"最佳实践"会与现有 governance 抢优先级 |
| GPT Researcher | llm-research-kb + karpathy-kb-research 是亲手搭的方法论中台，不被替代 |
| Systematic Debugging / Code Refactoring | 概念性 prompt 包；已有内置 `simplify` Skill + dev_log + 小步走 |
| Marketing Skills | CTO 路线，不在战场 |
| Changelog Maintenance | dev_log.md + WIP.md + 频繁 push 节奏更顺手 |
| Tavily MCP（暂） | WebSearch 当前够用，深度调研不卡再装 |
| Filesystem / Memory / Sequential Thinking MCP | Claude Code 已内置等价能力，装上反而占 token |
| brand-guidelines / theme-factory / slack-gif-creator / algorithmic-art | 场景不匹配 |
| **tmux 当 Skill 装** | 它是 brew CLI 工具，**不是 Skill**；有长任务再装 |

---

## 八、可迁移的评估方法论（课件核心）

### 8.1 三步评估法

1. **辨类型**：用第一节口诀，区分 Skill / MCP / Plugin / CLI
2. **核数据**：教程里的 stars / 安装数 —— **不能核实就当博主总结**，不入决策
3. **对账项目**：让 LLM 推荐具体落地时，必须拿真实 INDEX 对账，**警惕项目细节幻觉**

### 8.2 四个常见陷阱

| 陷阱 | 表现 | 防御 |
|---|---|---|
| 概念泛化 | 把所有"能让 AI 变强"的东西都叫 Skill | 区分四类 |
| 数据信仰 | 96k stars / 277k 安装等数字背书 | 不可核实 = 不入决策 |
| 上下文污染 | 推荐"批量装 4 个" Plugin/MCP | 一次装一个，验证后再下一个 |
| 细节幻觉 | LLM 编造项目状态来"贴身"匹配 | 拿真实 INDEX 对账 |
| **博主选择性盲区** | 视频列 15 个不等于全集 | **永远去官方仓库源核对** |

### 8.3 反向决策四问

每装一个工具前问自己：

- **Q1**：它和现有 governance（CLAUDE.md / system-prompt / feedback memory）冲突吗？
- **Q2**：它解决的是真痛点，还是潜在痛点？
- **Q3**：产出能进 dev_log / wiki，还是一次性产物？
- **Q4**：装完之后，**谁负责定期评估**它要不要留？

四问答不上 → 不装。

---

## 九、课件转化大纲

本文件 + skills-howto 可拆为 **3 个 30-45 分钟课时**：

| 课时 | 主题 | 用本文哪几节 | 学生练习 |
|------|------|------|---|
| **第 1 课：辨真伪** | 什么是 Skill？四类东西怎么分？ | §1（四类辨别）+ §4.1（视频真伪表） | 让学生评估一个 AI 工具教程，输出辨别表 |
| **第 2 课：避陷阱** | 网页端 Claude 怎么编故事？教程数字能信吗？ | §4.2 漏报教训 + §4.3 幻觉案例 + §8 方法论 | 学生用一个 LLM 假装专家做项目分析，发现幻觉 |
| **第 3 课：建体系** | 怎么挑、怎么装、怎么淘汰？ | §2 全清单 + §3 MCP + §6 路径 + skills-howto 操作 | 学生为自己一个项目挑 3 个 Skill，按反向决策四问验收 |

**课件叙事主线**：
> 一个 8 分钟 YouTube 视频 → 看似靠谱的 Claude 网页端分析 → 真实 INDEX 对账 + 官方仓库核对 → 砍掉错的、补上漏的 → 沉淀方法论。

---

## 十、本次评估产物

- ✅ skills.md v2（本文，官方源核对版）
- ✅ skills-howto.md（独立操作手册）
- ⏳ 阶段 A 五步实测后回填评分
- ⏳ 课件成型时拆为 3 课时

---

## 关联词条

- [[overview]] — Claude Code 整体架构
- [[skills-howto]] — Skill 操作手册（机制/安装/最小示例）
- [[hooks]] — Hooks 配置实战
- [[slash-commands]] — 命令速查
- [[workflow-patterns]] — 凯戈专属工作流
- [[claude-md-patterns]] — CLAUDE.md 最佳实践
- [[context-management]] — 上下文管理实战
