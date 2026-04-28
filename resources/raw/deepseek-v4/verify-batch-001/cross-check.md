# Cross-Check 核实报告 · DeepSeek V4 Task 2+3

**日期**：2026-04-27
**核实主体**：Claude Code CLI（claude-opus-4-6），WebFetch 工具
**SOP 依据**：[SOP_MULTI_AGENT_VERIFICATION_v1](../../../../../karpathy-kb-research/research/SOP_MULTI_AGENT_VERIFICATION_v1.md) 步骤 3+4

---

## 总判定

| AI | 整体判定 | 关键问题 |
|---|---|---|
| **Gemini** | ⚠️ 部分准确，关键处错误 | Cache hit 价格小数位错 10 倍、限时折扣截止日期错 26 天、若干 Gotcha 无官方依据 |
| **GPT** | ✅ 诚实弃权 | 明确说明无法联网，给出 `待查` 模板，未传播错误信息 |

**核心教训**：Gemini 自称"查阅了 2026-04-27 最新版官方文档"，但其训练数据截止在 4 月 26 日**降价之前**，导致 cache hit 价格错了一个数量级（详见下表 #1）。R-V3「数字类必走 Claude 核实」此次救场。

---

## Task 2 · 定价交叉对账

### #1 Cache Hit 价格（🚨 严重错误）

| 项 | Gemini | GPT | 官网实测 | 判定 |
|---|---|---|---|---|
| V4-Flash cache hit 输入 | $0.028/M | 待查 | **$0.0028/M** | Gemini 错 10 倍 |
| V4-Pro cache hit 输入 | $0.145/M | 待查 | **$0.0145/M** | Gemini 错 10 倍 |

**根因**：[Source: WebFetch https://api-docs.deepseek.com/quick_start/pricing] 明确写
> "cache hit pricing was recently reduced to one-tenth the original launch price effective April 26, 2026"

→ Gemini 拿的是 4 月 25 日及之前的发布价，错过 4 月 26 日（昨天）的降价。

### #2 V4-Pro 限时折扣截止日期（⚠️ 错误）

| 项 | Gemini | 官网实测 |
|---|---|---|
| 75% 折扣截止 | 2026-05-05 | **2026-05-31 15:59 UTC** |

[Source: WebFetch https://api-docs.deepseek.com/quick_start/pricing]

### #3 主输入/输出价（✅ 一致）

| 项 | Gemini | 官网实测 |
|---|---|---|
| V4-Flash 输入（cache miss） | $0.14/M | $0.14/M ✅ |
| V4-Flash 输出 | $0.28/M | $0.28/M ✅ |
| V4-Pro 输入（cache miss） | $1.74/M | $1.74/M ✅ |
| V4-Pro 输出 | $3.48/M | $3.48/M ✅ |
| 上下文窗口 | 1M | 1M ✅ |
| Max output | 未提 | 384K |

[Source: WebFetch https://api-docs.deepseek.com/quick_start/pricing]

### #4 V3 / V3.2 价格（⚠️ 需进一步核实）

- Gemini 报 V3/V3.2 输入 $0.27 / cache hit $0.07 / 输出 $1.10，但 WebFetch 当前 pricing 页未直接列出 V3/V3.2 行
- 官网仅提到 "deepseek-chat / deepseek-reasoner 将下线，目前已自动映射"
- **结论**：V3/V3.2 数字 `[STRICT-VERIFY]`，建议保留但标存疑

### #5 老模型映射（⚠️ Gemini 简化失真）

| 项 | Gemini | 官网实测 |
|---|---|---|
| `deepseek-chat` 映射 | → V4-Flash | → **V4-Flash 非思考模式** |
| `deepseek-reasoner` 映射 | → V4-Flash | → **V4-Flash 思考模式** |
| 下线日期 | 2026-07-24 ✅ | 2026/07/24 ✅ |

[Source: WebFetch https://api-docs.deepseek.com/]

---

## Task 3 · Claude Code 接入交叉对账

### #6 Base URL（🚨 GPT 错，Gemini 对）

| 项 | Gemini | GPT | 官网实测 |
|---|---|---|---|
| ANTHROPIC_BASE_URL | `https://api.deepseek.com/anthropic` ✅ | `https://api.deepseek.com/v4` ❌ | **`https://api.deepseek.com/anthropic`** |

[Source: WebFetch https://api-docs.deepseek.com/guides/anthropic_api]

### #7 环境变量名（⚠️ 不一致，需实测）

| 项 | Gemini | 官网 anthropic 集成页 |
|---|---|---|
| API Key 变量 | `ANTHROPIC_AUTH_TOKEN` | `ANTHROPIC_API_KEY` |

**结论**：[STRICT-VERIFY] — Claude Code CLI 在不同版本/不同来源中两个变量名都见过，建议凯戈实测哪个生效。从 Claude Code 官方文档语义来看，`ANTHROPIC_AUTH_TOKEN` 也是合法路径（用于第三方代理），**两者皆可，但优先用官网示范的 `ANTHROPIC_API_KEY`**。

[Source: WebFetch https://api-docs.deepseek.com/guides/anthropic_api]

### #8 子代理模型变量（⚠️ Gemini 单方）

- Gemini 给出 `CLAUDE_CODE_SUBAGENT_MODEL=deepseek-v4-flash`
- 官网未在 anthropic 集成页提及此变量
- **结论**：[STRICT-VERIFY] — 这是 Claude Code 自身的环境变量（用于 Task/Explore 子代理），与 DeepSeek 无直接关系。设置可行但属于 Claude Code 配置，不属于 DeepSeek 接入步骤。

### #9 已知 Gotcha（⚠️ Gemini 单方提供）

| Gotcha | 来源 | 判定 |
|---|---|---|
| XML Tool Call 漂移 | Gemini 单方 | [STRICT-VERIFY] 无官网章节支持 |
| Reasoning Token 4096 上限建议 | Gemini 单方 | [STRICT-VERIFY] CLAUDE.md 中 `thinking_max_tokens` 字段未在官方查到 |
| 400 Error reasoning_content 未透传 | Gemini 单方 | [STRICT-VERIFY] 合理猜测但无官方记录 |
| Base URL 必须带 `/anthropic` 后缀 | Gemini 单方 | ✅ 与官网 anthropic 端点一致，符合实情 |

→ Gotcha 类内容**可保留**为"社区经验提示"，但必须打 `[STRICT-VERIFY]`，不冒充官方。

### #10 安装与调用（✅ 与官网一致）

- `pip install anthropic` [Source: WebFetch https://api-docs.deepseek.com/guides/anthropic_api]
- `model="deepseek-v4-pro"` 调用名 ✅
- 不支持的模型名会自动 fallback 到 `deepseek-v4-flash` [Source: 同上]

---

## 幻觉归档（待写入 obsidian-llm-plugins.md 幻觉记录节）

```markdown
- **DeepSeek V4 Cache Hit 价格** — Gemini 在 2026-04-27 自称"查阅最新版"，给出 V4-Flash $0.028/M、V4-Pro $0.145/M，实际官网 [Source: WebFetch api-docs.deepseek.com/quick_start/pricing] 为 $0.0028/M 和 $0.0145/M，Gemini 漏掉了 4 月 26 日的"降至原价 1/10"调价（训练数据 cutoff 失明）
- **DeepSeek V4-Pro 限时折扣截止日期** — Gemini 报 2026-05-05，实际为 2026-05-31 15:59 UTC，错 26 天
- **CLAUDE_CODE_SUBAGENT_MODEL 为 DeepSeek 接入步骤** — Gemini 把它列入 DeepSeek 接入指南；实际这是 Claude Code 自身环境变量，与 DeepSeek 无关
- **XML Tool Call 漂移 / 400 Error / 4096 上限建议** — Gemini 给出具体 Gotcha 但无官方依据，属于"经验性猜测"包装成"已知问题"
```

---

## 最终入库决策

| 文件 | 内容来源 |
|---|---|
| `wiki/deepseek-v4-pricing.md` | 官网实测数据（含 Cache hit 修正、限时折扣截止 5/31）；V3/V3.2 对比段落标 STRICT-VERIFY |
| `wiki/deepseek-v4-claude-code-integration.md` | Gemini 经修正后的接入步骤 + 官网原文校对；环境变量名两选项并列；Gotcha 全标 STRICT-VERIFY |
| `wiki/deepseek-v4.md` 第 6 节 | 替换为修正后的官网价格表，加注 4 月 26 日 cache hit 降价里程碑 |
| `wiki/deepseek-v4.md` 第 10 节 | Task 2 / Task 3 标 ✅ 完成 |
| `karpathy/wiki/entities/obsidian-llm-plugins.md` | 幻觉记录节追加 4 条 |
