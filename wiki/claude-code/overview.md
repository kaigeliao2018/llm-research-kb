# Claude Code 整体架构与层级模型

> 研究日期：2026-04-26  
> 状态：实战总结，持续更新  
> 研究员：凯戈 + Claude

---

## 一、什么是 Claude Code

Claude Code 是 Anthropic 官方发布的 CLI 工具，让你在终端直接与 Claude 协作完成软件工程任务。核心特点：

- **完整工具权限**：读写文件、执行 bash、搜索代码、调用 MCP 服务
- **持久会话**：可续接（`claude -c`）、可压缩（`/compact`）、可跨会话记忆（memory 系统）
- **可编程**：Hooks 监听工具调用事件，自定义命令封装重复工作流
- **多端统一**：CLI、Mac/Windows 桌面 App、claude.ai/code、VS Code / JetBrains 插件

---

## 二、使用层级模型

```
层级 1 — 对话式（基础）
  直接对话，让 Claude 读文件、改代码、解释问题
  适合：偶尔使用、简单任务

层级 2 — 工具链（进阶）
  用 @引用 减少来回，用 /compact 管理上下文，用 CLAUDE.md 写项目规范
  适合：日常开发辅助

层级 3 — 自动化（高效）
  Hooks 让规则 100% 执行，自定义命令封装重复工作流
  适合：多项目维护、有固定 SOP 的工作

层级 4 — 系统化（专家）
  memory 系统跨会话记忆，计划文件管理复杂任务，多 Agent 并行
  适合：复杂项目、团队协作
```

**凯戈当前状态（2026-04-26）**：工作复杂度在层级 3-4，工具使用偏层级 1-2，此知识库目标是补上这个差距。

---

## 三、核心组件速查

| 组件 | 位置 | 用途 |
|------|------|------|
| CLAUDE.md | `~/.claude/CLAUDE.md`（全局）或 `.claude/CLAUDE.md`（项目） | 永久指令，每次会话自动加载 |
| settings.json | `~/.claude/settings.json` | Hooks、权限、模型配置 |
| 自定义命令 | `~/.claude/commands/*.md`（全局）或 `.claude/commands/*.md`（项目） | 封装可复用工作流 |
| memory 系统 | `~/.claude/projects/*/memory/` | 跨会话记忆，自动注入上下文 |
| 计划文件 | `~/.claude/plans/*.md` | 管理复杂任务的结构化计划 |
| MCP 服务 | settings.json 中配置 | 接入外部工具（Figma、Supabase 等） |

---

## 四、Claude Code vs 其他 AI 编程工具

| 工具 | 定位 | 优势 | 劣势 |
|------|------|------|------|
| Claude Code | CLI + 全工具权限 | 最强推理，完整系统访问 | 需要终端习惯 |
| Cursor | IDE 集成 | 代码补全流畅 | 上下文窗口小，工具权限受限 |
| Cline | VS Code 插件 | 开源可定制 | 依赖 IDE 环境 |
| Gemini CLI | Google 生态 | 超大上下文（100万 token） | 复杂推理弱于 Claude |

**凯戈工作流**：Claude Code CLI 主战场，视觉任务（图片确认）用 Gemini。

---

## 五、启动方式

```bash
# 新会话
claude

# 续接上次会话（最重要）
claude -c

# 指定目录启动
claude --dir ~/my-project

# 后台 headless 模式（脚本用）
claude -p "生成本周周报" --output-format json
```

---

## 六、模型选择

| 模型 | 场景 | 命令 |
|------|------|------|
| Sonnet 4.6（默认） | 日常开发、代码修改 | 默认 |
| Opus 4.6 | 架构决策、复杂分析 | `/model claude-opus-4-6` |
| Haiku 4.5 | 快速简单任务 | `/model claude-haiku-4-5` |

Fast Mode：`/fast` 切换（同 Opus 4.6 模型，输出更快）

---

## 关联词条

- [[context-management]] — 上下文管理实战
- [[claude-md-patterns]] — CLAUDE.md 最佳实践
- [[hooks]] — Hooks 配置实战
- [[slash-commands]] — 命令速查
- [[workflow-patterns]] — 凯戈专属工作流
- [[skills]] — Skills 实战指南（含视频评估案例 + 课件素材）
