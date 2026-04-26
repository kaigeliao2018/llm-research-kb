# 凯戈专属工作流模式

> 研究日期：2026-04-26  
> 状态：实战总结，持续迭代  
> 研究员：凯戈 + Claude

---

## 一、工作流全景

```
开工 → 同步状态 → 执行任务 → 中途管理 → 收工
  ↑                                           ↓
  └─────────── 小步走（WIP + push）───────────┘
```

**小步走铁律**：每完成一个小任务就 push，防止上下文满载丢状态。

---

## 二、标准开工流程

```
1. claude -c                     # 续接，或 claude 新开
2. [自动] 上线序列（CLAUDE.md 驱动）
   - 输出「收到，凯戈！上线初始化中...」
   - CronCreate：30分钟 git push 提醒
   - 读 ~/kaige-brain/INDEX.md
   - 汇报状态，询问是否 git pull
3. git pull <活跃项目>           # 确认后执行
4. 读 WIP.md（当前项目）         # 确认上次停在哪里
5. 开工
```

---

## 三、任务执行模式

### 3.1 简单任务（< 30 分钟）

直接对话执行，完成后：
- 更新 WIP.md
- git commit + push
- 更新 dev_log.md

### 3.2 复杂任务（多步骤）

```
1. Shift+Tab（计划模式）— 让 Claude 先列出完整计划
2. 凯戈确认计划
3. 分步执行，每步完成后 WIP.md 打勾
4. 中途上下文 75%+ → /compact
5. 分批 push，不攒到最后
```

### 3.3 跨会话任务

```
会话1：
  - 执行到上下文满载
  - 把进度写入 ~/.claude/plans/<name>.md
  - push 代码
  - 新开会话

会话2：
  - claude -c（或 claude 新开）
  - 「读计划文件，继续上次工作」
  - Claude 读计划文件，从断点继续
```

---

## 四、战情报告工作流（VEX 世锦赛）

**触发**：`/battle-report <队号>`

**完整流程**：
```
1. 查 worlds-2026.json → 确认队伍所在分区
2. 读 skills CSV → 技能赛排名和分数
3. fetch_matches.py → 拉 RobotEvents API 资格赛数据（round=2）
4. 拉决赛数据（round=15）
5. analyze.py → 生成统计（胜率、得分分布、关键对手）
6. 生成 HTML 报告（中英双语）到 reports/
7. 复制到桌面
8. 询问凯戈：commit + push？
```

**数据源铁律**：
- 技能赛：本地 CSV（赛前下载）
- 资格赛：API 实时拉取（round=2）
- 决赛：API 实时拉取（round=15）
- 不得混用，不得猜测

---

## 五、KB 入库工作流

**触发**：`/kb-ingest <素材描述>` 或手动

**完整流程**：
```
1. 盘点 — 列出所有原始素材，建对账表
   （铁律：产出前先列素材对账表，不靠记忆编造）
2. 提案 — 给出 wiki 文件结构方案，等凯戈确认
3. 缝合 — 按确认方案写入，维护 [[双链]]
4. 校验 — 检查 broken link、frontmatter、格式
5. 更新 dev_log.md
6. git commit + push
```

**图片入库铁律（vex-iq-kb）**：
- 必须经 Gemini 视觉确认，不猜图片内容
- 路径格式：`wiki/rules/en/images/`
- 一张图最多读一次（防 dimension limit 错误）

---

## 六、知识库研究工作流（llm-research-kb）

**双端分工**：
- Mac（Claude Code CLI）：入库落盘，写 wiki，git push
- 网页版 Projects（外勤）：读 PDF、爬中文、搜集素材

**研究铁律**：
- 严禁猜想：没有来源的内容不入库
- 严禁以偏概全：一个例子不代表普遍规律
- 规则推广前：先局部抽样验证

---

## 七、Multi-Agent 工作流

**使用场景**：
- 独立任务可以并行执行
- 一个 Agent 研究，一个 Agent 写代码

**在 Claude Code 中使用**：
```bash
# 主 Agent 对话中
claude agents  # 查看并行 Agent
```

**凯戈适用场景**：
- 研究 + 入库并行（一个查资料，一个写 wiki）
- 多个 VEX 队伍战情报告并行生成

---

## 八、效率快捷键

| 快捷键 | 功能 |
|--------|------|
| `Option + T` | 扩展思考模式（架构决策用） |
| `Shift + Tab` | 计划模式（复杂任务前用） |
| `Ctrl + C` | 中断当前操作 |
| `↑` | 历史命令 |

---

## 九、工作流文件系统

| 文件 | 位置 | 用途 |
|------|------|------|
| WIP.md | 项目根目录 | 当前进行中任务状态，每次会话更新 |
| dev_log.md | 项目根目录 | 已完成工作流水账，只追加 |
| INDEX.md | ~/kaige-brain/ | 所有项目总览，全局真相源 |
| 计划文件 | ~/.claude/plans/ | 复杂任务断点续接 |
| memory/ | ~/.claude/projects/*/memory/ | 跨会话记忆 |

---

## 关联词条

- [[overview]] — Claude Code 整体架构
- [[context-management]] — 上下文管理细则
- [[hooks]] — Hooks 强化工作流执行率
- [[slash-commands]] — /battle-report 等封装命令
