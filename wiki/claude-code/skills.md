# Claude Code Skills 实战指南

> 研究日期：2026-05-05
> 状态：方法论沉淀 + 案例研究
> 研究员：凯戈 + Claude（Opus 4）
> 双重定位：知识库词条 + 课程课件素材源

---

## 一、概念澄清：什么才叫 Skill

Claude Code 生态里"Skill"被泛化使用，**4 类东西经常被混为一谈**。装错了不仅没用，还会污染上下文窗口、稀释 CLAUDE.md 优先级。

| 类型 | 本质 | 安装方式 | 上下文占用 | 例子 |
|------|------|----------|-----------|------|
| **Anthropic 官方 Skill** | `SKILL.md` 文件机制，按需加载到上下文 | `~/.claude/skills/` 或项目 `.claude/skills/` | 中（按需） | PDF Processing、DOCX、Skill Creator、Frontend Design |
| **MCP Server** | Model Context Protocol，独立进程提供工具 | `settings.json` 配置 + 启动服务 | 低（工具列表） | Context7、Tavily、Figma MCP、Supabase MCP |
| **社区 Plugin / Prompt 包** | 第三方 prompt 集合，本质是规范注入 | git clone + 引用 / 复制到 CLAUDE.md | 高（常驻） | Superpowers、Systematic Debugging |
| **外部 CLI 工具** | 操作系统级工具，与 Claude 无关 | `brew install` / `apt install` | 零 | tmux、jq、ripgrep |

**辨别口诀**：
- 看到 `SKILL.md` 文件 → 真 Skill
- 看到 `mcpServers` 配置 → MCP（不是 Skill）
- 看到只是一份 markdown 规范 → Plugin
- 看到 `brew install` → CLI 工具

**为什么重要**：四类东西的安装方式、上下文成本、维护代价完全不同。混着推荐 = 误导决策。

---

## 二、案例研究：8 分钟视频"15 个顶级 Skills"评估（2026-05）

### 2.1 信息源
- YouTube 8'30" 视频 → Gemini 转文字 → 网页端 Claude 分析 → CLI Claude 二次审查（本文）
- 视频按"基础 4 / 效率 7 / 进阶 4"三梯队推荐 15 个 Skills

### 2.2 真伪辨别（用第一节的口诀拆）

| 视频归类为 Skill | 实际类型 | 备注 |
|---|---|---|
| PDF Processing | ✅ 官方 Skill | Anthropic 出品 |
| DOCX | ✅ 官方 Skill | Anthropic 出品 |
| Frontend Design | ✅ 官方 Skill | Anthropic 出品 |
| Skill Creator | ✅ 官方 Skill（元 Skill） | Anthropic 出品 |
| Context7 | ❌ MCP Server | 不是 Skill |
| Tavily | ❌ MCP / API | 不是 Skill |
| tmux | ❌ CLI 工具 | 完全不是 Claude 概念 |
| GPT Researcher | ❌ 独立开源项目 | 不是 Skill |
| agent-browser | ⚠️ 社区项目 | 需独立验证 |
| Superpowers | ⚠️ 社区 Prompt 包 | "96k stars" 无法核实 |
| Systematic Debugging | ⚠️ 社区 prompt | 概念性内容 |
| Code Refactoring | ⚠️ 社区 prompt | 同上 |
| Marketing Skills | ⚠️ 社区包 | |
| Webapp Testing | ⚠️ 社区 | |
| Changelog Maintenance | ⚠️ 社区 | |

**结论**：视频里**真正的"Skill"只有 4 个**，其余 11 个分类都不准确。

### 2.3 视频 4 条落地建议评分

- ✅ 分批安装 — 对的，但"先装第一梯队 4 个用一周"对凯戈不适用（见 2.4）
- ✅ 定期清理 — 对，多项目体系尤其重要
- ✅ 看重出身 — 官方 > 高 star > 社区杂牌
- ✅ 学会组合 — 抽象正确

### 2.4 网页端 Claude 分析的硬伤（重要教训）

让网页端 Claude 结合"凯戈现有项目"给建议时，它**编造了大量不存在的项目细节**：

| 网页端说法 | 实际情况（INDEX 对账） |
|---|---|
| "VEX-IQ-KB Day 4 / MinerU 一直失败" | vex-iq-kb 没有 Day 几迭代；MinerU 在 karpathy-kb-research 评估过且**没选用**（选了 fitz）|
| "LEVEL3-GOVERNANCE-FRAMEWORK.md + 4 份战略文档" | 不存在 |
| "Phase 2 四 Agent (Analyzer/Packer/Designer/Integrator)" | 虚构架构 |
| "magikid-projects-hub 集中管理 6 项目" | hub 是"按需"，活跃项目 8+ |

**教训（必须沉淀）**：
> LLM 拿到片段信息后，会**幻觉化项目细节**来让建议显得"贴身"。
> 必须拿真实 INDEX 对账，否则建议建立在沙堆上。

---

## 三、凯戈当前已有的 Skills（系统检测，2026-05-05）

```
当前会话已加载：
├── update-config         — 配置 settings.json
├── keybindings-help      — 键盘快捷键
├── simplify              — 代码审查 / 简化
├── loop                  — 定时循环任务
├── schedule              — 远程定时 agent
├── claude-api            — Claude API / SDK 应用
├── battle-report         — VEX 世锦赛战情报告（凯戈自建）
├── claude-hud:setup      — statusline 配置
└── claude-hud:configure  — HUD 显示选项
```

**关键认知**：`battle-report` 是凯戈自建的项目专属 Skill —— **Skill Creator 的能力你已经在用**，不是"待入门状态"。

---

## 四、Skills × 凯戈项目匹配矩阵

按 INDEX（2026-05-01）实际项目状态评分。

### 🟢 高价值 + 真匹配（立即可装）

| Skill | 类型 | 落地项目 | 用途 |
|---|---|---|---|
| **PDF Processing**（官方） | Skill | vex-iq-kb / llm-research-kb / pegatron-rma-project | 新赛季 Level Up Game Manual v0.1 解析对比；白皮书；客户协议 |
| **Context7** | MCP | vex-iq-build-assistant / vexiq-ai-coder | Three.js / Electron / WebSerial / Recharts API 防幻觉 |
| **DOCX**（官方） | Skill | pegatron-rma-project / ai-teaching-kb | 客户交付报告；课程报告标准化 |

### 🟡 按需触发（不预装）

| Skill | 触发条件 |
|---|---|
| Frontend Design（官方） | vex-iq-kb app v3.0 / vexiq-ai-coder UI 升级 |
| agent-browser | Pegatron RMA 真要做后台抓取演示时 |
| Tavily（MCP） | llm-research-kb 调研深度不够时 |

### 🔴 不装清单（理由比"不推荐"重要）

| Skill | 不装的真正原因 |
|---|---|
| Superpowers | 已有 `system-prompt.md` + `coding-standards.md` + 8+ 条 feedback memory（研究铁律 / 真理第一 / 小步走 / 不脑补 / 用户视角汇报）。再注入"最佳实践"会与现有 governance 抢优先级 |
| GPT Researcher | llm-research-kb + karpathy-kb-research 是亲手搭的方法论中台，外部 Researcher 替代不了 SOP_MULTI_AGENT_VERIFICATION |
| Systematic Debugging / Code Refactoring | 概念性 prompt 包；已有内置 `simplify` Skill + dev_log + 小步走机制 |
| Marketing Skills | CTO 路线，不在战场 |
| Changelog Maintenance | `dev_log.md` + `WIP.md` + 频繁 push 节奏更顺手 |
| Webapp Testing | Electron / 静态部署 / Markdown 为主，Web 测试非主战场 |
| **tmux 当 Skill 装** | 它是 brew CLI 工具，**不是 Skill**；有长任务再装 |

---

## 五、学习与实施路径（小步走）

**第 1 步（本周）**：装 PDF Processing 官方 Skill
- 落地：vex-iq-kb 解析新赛季 Game Manual v0.1
- 评分维度：图像提取 / 表格保留 / 章节切分 / 与现有 fitz 产出对比
- 产出：本文件追加"PDF Processing 实测评分"段

**第 2 步（PDF 验完后）**：装 Context7 MCP
- 落地：vex-iq-build-assistant feature/analyzer 分支验证 Three.js API
- 产出：实测 vs 凭记忆写代码的差异记录

**第 3 步（按项目节奏触发）**：
- Pegatron 进入交付期 → DOCX
- vex-iq-kb v3.0 启动 → Frontend Design

**第 4 步（沉淀）**：每验完一个 Skill，更新本文件评分；不通过的立刻移除，避免上下文污染。

---

## 六、可迁移的评估方法论（课件核心）

这套思路可套用到**任何 AI 工具/教程评估**，独立于 Skills 主题。

### 6.1 三步评估法

1. **辨类型**：用第一节口诀，区分 Skill / MCP / Plugin / CLI
2. **核数据**：教程里的 stars / 安装数 / 用户数 — **不能核实就当博主总结**，不入决策
3. **对账项目**：让 LLM 推荐具体落地时，必须拿真实 INDEX 对账，**警惕项目细节幻觉**

### 6.2 四个常见陷阱

| 陷阱 | 表现 | 防御 |
|---|---|---|
| 概念泛化 | 把所有"能让 AI 变强"的东西都叫 Skill | 区分四类 |
| 数据信仰 | 96k stars / 277k 安装等数字背书 | 不可核实 = 不入决策 |
| 上下文污染 | 推荐"批量装 4 个" | 一次装一个，验证后再下一个 |
| 细节幻觉 | LLM 编造项目状态来"贴身"匹配 | 用真实 INDEX 对账 |

### 6.3 反向决策四问

每装一个 Skill 前问自己：

- **Q1**：它和我现有 governance（CLAUDE.md / system-prompt / feedback memory）**冲突吗**？
- **Q2**：它解决的是**真痛点**，还是潜在痛点？
- **Q3**：它的产出能进我的 dev_log / wiki，还是**一次性产物**？
- **Q4**：装完之后，**谁负责定期评估**它要不要留？

四问答不上 → 不装。

---

## 七、课件转化建议（未来做课程时用）

本文件可拆为 3 个 30 分钟课时：

| 课时 | 主题 | 用本文哪几节 |
|------|------|------|
| **第 1 课：辨真伪** | 什么是 Skill？四类东西怎么分？ | 第 1 节 + 第 2 节真伪表 |
| **第 2 课：避陷阱** | 网页端 Claude 怎么编故事？教程里的数字能信吗？ | 第 2.4 节（幻觉案例）+ 第 6 节方法论 |
| **第 3 课：建体系** | 怎么挑、怎么装、怎么淘汰？ | 第 4 节矩阵 + 第 5 节路径 + 第 6.3 反向决策 |

**课件叙事主线**：
> 一个 8 分钟的 YouTube 视频 → 一个看似靠谱的 Claude 网页端分析 → 真实 INDEX 对账 → 砍掉 11/15 → 沉淀方法论。

---

## 八、本次评估产物

- ✅ 本文件：知识沉淀 + 课件素材源
- ⏳ 第 1 步 PDF Processing 实测后回填评分
- ⏳ 课件成型时拆为 3 课时

---

## 关联词条

- [[overview]] — Claude Code 整体架构
- [[hooks]] — Hooks 配置实战
- [[slash-commands]] — 命令速查
- [[workflow-patterns]] — 凯戈专属工作流
- [[claude-md-patterns]] — CLAUDE.md 最佳实践
- [[context-management]] — 上下文管理实战
