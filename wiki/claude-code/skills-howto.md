# Claude Code Skill 操作手册

> 研究日期：2026-05-05
> 状态：官方文档核对版
> 用途：工具书 / 学生查阅 / 实操参考
> 配套：[[skills]]（概念 + 案例 + 项目矩阵）

---

## 一、Skill 标准目录结构

```
skill-name/
├── SKILL.md          # 必须，入口文件
├── scripts/          # 可选，可执行脚本（任意语言）
├── references/       # 可选，按需加载的长文档
└── assets/           # 可选，模板/图片等
```

**SKILL.md frontmatter 字段**（[官方 best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)）：

| 字段 | 必填 | 限制 | 说明 |
|---|---|---|---|
| `name` | ✅ | ≤64 字符，仅小写字母/数字/连字符 | Skill 标识符 |
| `description` | ✅ | ≤1024 字符 | **第三人称**写"做什么 + 何时用"。这是 Claude 决定是否激活该 Skill 的**唯一信号** |

⚠️ 其他字段（如 `allowed-tools`、`license`）社区文档有提及，官方页未独立列出，**以官方仓库 [anthropics/skills](https://github.com/anthropics/skills) template 为准**。

---

## 二、加载机制（progressive disclosure）

这是 Skill 与 MCP / Plugin 的**核心区别**：

| 阶段 | 加载内容 | 上下文成本 |
|---|---|---|
| 会话启动 | **仅注入所有 Skill 的 frontmatter** | ~70-150 tokens / Skill |
| Claude 命中 description | 加载该 Skill 的 SKILL.md 正文 | <5k tokens |
| 需要长文档时 | 按需读 references/ 子目录 | 按需 |

**算笔账**：装 10 个 Skill，启动开销 ~1000 tokens；只命中 1 个，加载 ~6k tokens。
对比 MCP：每个 MCP 启动即注入完整 tool schema，常驻成本远高。

**铁律**：SKILL.md 正文 ≤500 行，超出拆 references/。

来源：[官方 SKILL 文档](https://code.claude.com/docs/en/skills)

---

## 三、安装路径速查

| 类型 | 路径 | 用途 |
|---|---|---|
| 个人全局 Skill | `~/.claude/skills/` | 跨项目通用（凯戈推荐主放这里） |
| 项目专属 Skill | `<repo>/.claude/skills/` | 仅特定项目用 |
| 插件市场缓存 | `~/.claude/plugins/cache/` | 自动管理，缓存异常时清掉 |
| 已安装市场 | `~/.claude/plugins/marketplaces/` | `/plugin marketplace list` 可见 |

---

## 四、安装官方 Skill（命令）

### 4.1 添加 Anthropic 官方 marketplace

```
/plugin marketplace add anthropics/skills
```

### 4.2 安装单个 Skill

```
/plugin install pdf@anthropics
/plugin install xlsx@anthropics
/plugin install webapp-testing@anthropics
```

### 4.3 重启加载

```
/reload-plugins
```

或重开会话。

### 4.4 查看已安装

```
/plugin marketplace list
/plugin list
```

### 4.5 故障排查

```bash
# 缓存异常时
rm -rf ~/.claude/plugins/cache
# 重启 Claude Code
```

⚠️ marketplace slug 名称（`@anthropics`）需以 `/plugin marketplace list` 实际值为准——**不要凭记忆抄命令**。

---

## 五、Skill vs MCP vs Slash Command 对比表

| 维度 | Skill | MCP | Slash Command |
|---|---|---|---|
| **本质** | 模型读的"说明书 + 资源包" | 外部工具/数据服务器 | 用户触发的 prompt 模板 |
| **谁触发** | Claude 自动按 description 匹配 | Claude 调用 tool | 用户手动输入 `/xxx` |
| **加载** | 渐进式，仅注入 metadata | 启动即注入全部 tool schema（贵） | 不占启动 context |
| **配置文件** | `SKILL.md` | `mcp.json` + server 进程 | `.claude/commands/xxx.md` |
| **何时用** | 流程/方法论/领域知识 | 真实 IO（DB、API、爬虫、浏览器） | 高频固定指令快捷键 |
| **凯戈例子** | `battle-report`（自建）、`pdf` | GitHub MCP、Context7 | `/commit`（如果做） |

**决策树**：

```
要解决的问题是什么？
├── "重复的工作流程/方法论"        → Skill
├── "需要操作外部系统（DB/网页/API）" → MCP
└── "经常打的同一段命令/prompt"     → Slash Command
```

---

## 六、识别凯戈现有的 `battle-report`

从 SkillTool 列表看注册名为 `battle-report`，是 **Skill**（Claude 按 description 自动激活）——不是 Command。

如果想强制用户手动触发，可再加一个：
```
.claude/commands/battle-report.md  →  内容调用 Skill
```

这就是 **Skill + Command 配合**的常见模式。

---

## 七、Skill Creator 用法（自建 Skill 的入口）

**输入**：自然语言描述 + 现有素材路径
**输出**：完整 `skill-name/` 目录骨架（SKILL.md + 子目录），自动校验 frontmatter
**触发**：直接说"用 skill-creator 帮我做一个 X Skill"

### 凯戈未来候选 Skill

按 INDEX 实际工作流，可封装的候选：

| 候选 Skill | 封装的工作流 | 价值 |
|---|---|---|
| **kb-ingestor** | PDF → game-specific-definitions → wiki 入库 | vex-iq-kb 每赛季都要做，已成型 |
| **session-closer** | INDEX.md 更新 + git push + memory 同步 | 每次收工都要做 |
| **wiki-publisher** | 中文 wiki → 双语转换 + 图片渲染 + push | vex-iq-kb v2.0 已实战过 |
| **dev-log-writer** | 当日 commit → dev_log.md 自动生成 | 多项目通用 |

---

## 八、最小可运行示例（10 行）

```markdown
---
name: greet-kaige
description: Greets Kaige in Chinese with current date. Use when user says hi or 早.
---
# Greet Kaige
1. 读取系统当前日期
2. 输出："凯戈早，今天是 YYYY-MM-DD"
```

存为 `~/.claude/skills/greet-kaige/SKILL.md` → 重启会话即生效。
说"hi" 或"早"测试是否激活。

---

## 九、写 Skill 的 5 条铁律

1. **description 决定生死**：写得不准，Claude 永远不激活；写得太宽，会过度激活
2. **第三人称**：description 写"This skill helps with X. Use when..."，不要"我帮你 X"
3. **正文 ≤500 行**：超出拆 references/
4. **scripts 给确定性**：复杂逻辑用脚本，不要让 Claude 现场推导
5. **测试激活率**：装好后用各种说法测试 Claude 是否能识别该用它

---

## 十、Skill 评估清单（课件用）

学生交作业前自检：

- [ ] frontmatter 两个必填字段格式正确
- [ ] description 第三人称、含"做什么 + 何时用"
- [ ] SKILL.md 正文 ≤500 行
- [ ] 至少 3 种自然说法都能让 Claude 命中
- [ ] 不与现有 Skill description 高度重叠

---

## 关联词条

- [[skills]] — Skills 实战指南（概念 + 项目矩阵 + 视频案例）
- [[slash-commands]] — Slash Command 完整速查
- [[hooks]] — Hooks 配置（Skill 之外的另一种自动化）
- [[overview]] — Claude Code 整体架构

---

## 来源

- [Extend Claude with skills (Claude Code Docs)](https://code.claude.com/docs/en/skills)
- [Agent Skills overview (Claude API Docs)](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview)
- [Skill authoring best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)
- [anthropics/skills (官方源仓库)](https://github.com/anthropics/skills)
- [skill-creator SKILL.md](https://github.com/anthropics/skills/blob/main/skills/skill-creator/SKILL.md)
- [Discover and install plugins](https://code.claude.com/docs/en/discover-plugins)
- [Equipping agents with Agent Skills (Anthropic engineering)](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)
