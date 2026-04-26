# Claude Code Slash Commands 速查

> 研究日期：2026-04-26  
> 状态：内置命令速查 + 凯戈自定义命令  
> 研究员：凯戈 + Claude

---

## 一、内置命令速查表

| 命令 | 用途 | 使用频率 |
|------|------|----------|
| `/help` | 查看所有可用命令 | 偶尔 |
| `/model <模型ID>` | 切换模型 | 按需 |
| `/compact` | 压缩上下文（75%+ 时用） | 常用 |
| `/clear` | 清空上下文（任务切换时用） | 常用 |
| `/config` | 查看/修改配置 | 偶尔 |
| `/mcp` | 查看已连接的 MCP 服务 | 偶尔 |
| `/cost` | 查看本次会话的 Token 消耗 | 偶尔 |
| `/doctor` | 诊断 Claude Code 环境问题 | 偶尔 |
| `/fast` | 切换 Fast Mode（更快输出） | 按需 |

---

## 二、模型切换

```bash
/model claude-opus-4-6      # 最强推理，架构决策用
/model claude-sonnet-4-6    # 默认，日常开发
/model claude-haiku-4-5     # 最快，简单任务
```

---

## 三、自定义命令

### 3.1 创建方式

```
全局命令：~/.claude/commands/<name>.md
项目命令：<项目>/.claude/commands/<name>.md
```

调用：输入 `/<name>` 即触发。

### 3.2 文件格式

```markdown
---
description: "命令说明（显示在 /help 列表里）"
---

[命令正文，可以包含任意 Markdown 和指令]

参数用 $ARGUMENTS 占位，例如：分析队伍 $ARGUMENTS 的战情。
```

---

## 四、凯戈现有自定义命令

### `/battle-report`

文件：`~/.claude/commands/battle-report.md`

```markdown
---
description: "生成 VEX 世锦赛队伍战情报告（中英双语）"
---

分析队伍 $ARGUMENTS 的世锦赛战情，按以下步骤执行：
1. 在 worlds-2026.json 中查找队伍所在分区
2. 在对应 skills CSV 中获取技能赛数据
3. 用 fetch_matches.py 拉取 API 数据（注意限速）
4. 运行 analyze.py 生成统计
5. 生成中英双语 HTML 报告到 reports/ 目录
6. 复制到桌面
7. 询问是否 commit + push
```

使用：`/battle-report 2166B`

---

## 五、待建自定义命令

### `/kb-ingest`（待建）

封装知识库入库 SOP（盘点→提案→缝合→校验），用于 llm-research-kb、vex-iq-kb 等。

```markdown
---
description: "知识库入库 SOP：盘点素材→提案→缝合→校验"
---

对素材 $ARGUMENTS 执行标准入库流程：
1. 盘点：列出所有原始素材，建素材对账表
2. 提案：给出 wiki 文件结构和标题方案，等凯戈确认
3. 缝合：按确认方案写入 wiki，维护双链
4. 校验：检查 broken link、frontmatter、格式
5. 更新 dev_log.md
6. git add + commit + push
```

### `/worlds-analysis`（待建）

```markdown
---
description: "VEX 世锦赛分区赛情分析"
---

分析分区 $ARGUMENTS 的整体赛情，生成分区排名报告。
```

---

## 六、技巧

### 6.1 命令支持多行指令

自定义命令正文可以写完整的多步骤流程，Claude 会按顺序执行。

### 6.2 命令可以引用文件

```markdown
先读 @~/.claude/kaige-brain/INDEX.md，然后...
```

### 6.3 全局 vs 项目命令选择原则

- 跨项目通用（战情报告、KB入库）→ 全局命令（`~/.claude/commands/`）
- 项目特定（项目特有 SOP）→ 项目命令（`.claude/commands/`）

---

## 关联词条

- [[overview]] — Claude Code 整体架构
- [[hooks]] — Hooks 与命令协同
- [[workflow-patterns]] — 凯戈工作流中的命令使用
