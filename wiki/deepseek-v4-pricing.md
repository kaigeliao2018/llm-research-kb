---
name: deepseek-v4-pricing
description: DeepSeek V4 定价表（官网 WebFetch 实测，含 2026-04-26 cache hit 降价里程碑）
date: 2026-04-27
sources:
  - 官网定价页: https://api-docs.deepseek.com/quick_start/pricing
  - 官网模型概览: https://api-docs.deepseek.com/
  - 多 Agent 验证批次: resources/raw/deepseek-v4/verify-batch-001/
verification: 双 Agent 派单 (Gemini + GPT) → Claude WebFetch 终审；详见 cross-check.md
---

# DeepSeek V4 定价表（2026-04-27 实测）

> **数据来源**：Claude Code WebFetch `api-docs.deepseek.com/quick_start/pricing`，时间戳 2026-04-27
> **核实流程**：[SOP_MULTI_AGENT_VERIFICATION_v1](../../karpathy-kb-research/research/SOP_MULTI_AGENT_VERIFICATION_v1.md)

---

## 一、当前在售模型

| 模型 | 输入 cache miss | 输入 cache hit | 输出 | 上下文 | Max output |
|------|---------------|---------------|------|--------|-----------|
| **deepseek-v4-flash** | $0.14/M | **$0.0028/M** | $0.28/M | 1M | 384K |
| **deepseek-v4-pro** | $1.74/M | **$0.0145/M** | $3.48/M | 1M | 384K |

[Source: WebFetch https://api-docs.deepseek.com/quick_start/pricing]

### V4-Pro 当前限时折扣（75% off）

| 项 | 折扣价 |
|---|---|
| 输入 cache miss | $0.435/M |
| 输入 cache hit | $0.003625/M |
| 输出 | $0.87/M |

**截止时间**：**2026-05-31 15:59 UTC**（约北京时间 2026-05-31 23:59）
[Source: WebFetch https://api-docs.deepseek.com/quick_start/pricing]

---

## 二、关键里程碑：2026-04-26 Cache Hit 降价

官网 footnote 明确指出：

> "cache hit pricing was recently reduced to **one-tenth** the original launch price effective **April 26, 2026**"

**含义**：
- 原始发布价（2026-04-24 V4 上线时）的 cache hit 是当前价的 **10 倍**
- 老资料（含 Simon Willison 4-24 博客及 4-25 之前的综述）若提到 cache hit，应**乘以 1/10** 才是当前现价
- 这是本次定价表与多家二手综述对不上的根本原因

[Source: WebFetch https://api-docs.deepseek.com/quick_start/pricing]

---

## 三、Simon Willison 数字判定

| Simon 给出 | 判定 |
|---|---|
| V4-Flash 输入 $0.14 / 输出 $0.28 | ✅ 准确（与官网当前现价一致） |
| V4-Pro 输入 $1.74 / 输出 $3.48 | ✅ 准确 |

→ Simon 给的是**正常档（cache miss）+ 折扣未应用**的价格，准确。

---

## 四、老模型（待退役）

| 模型 | 状态 | 映射 |
|------|------|------|
| `deepseek-chat` | 2026-07-24 下线 | 当前自动映射到 **V4-Flash 非思考模式** |
| `deepseek-reasoner` | 2026-07-24 下线 | 当前自动映射到 **V4-Flash 思考模式** |

[Source: WebFetch https://api-docs.deepseek.com/]

**迁移建议**（凯戈生产环境）：
- 7 月前显式改为 `deepseek-v4-flash` 或 `deepseek-v4-pro`
- 思考模式调用：使用 `deepseek-v4-flash` + thinking 参数（具体参数名需另行查 API 参考）

---

## 五、V3 / V3.2 历史价格 [STRICT-VERIFY]

> ⚠️ 本节数字未在 WebFetch 当前 pricing 页直接确认，仅来自 Gemini 单方报告，保留备查

| 模型 | 输入 cache miss | 输入 cache hit | 输出 |
|------|---------------|---------------|------|
| V3 / V3.2 | $0.27/M [STRICT-VERIFY] | $0.07/M [STRICT-VERIFY] | $1.10/M [STRICT-VERIFY] |

**核实建议**：通过 Wayback Machine 查 2026-04-23 之前的 pricing 页快照即可确认。

---

## 六、定价对比坐标

- **V4-Flash 输入 $0.14** —— 当前最便宜的小型前沿模型档位
- **V4-Pro 输入 $1.74** —— 大型前沿模型最便宜一档
- **Cache hit 99% 折扣** —— 长上下文重复调用场景（Claude Code、Obsidian KB）成本断崖式下降

---

## 七、版本历史

| 日期 | 来源 | 变更 |
|------|------|------|
| 2026-04-27 | 双 Agent 验证 + WebFetch 终审 | 首版入库；修正 cache hit 价格（Gemini 报错 10 倍）；修正限时折扣截止日（Gemini 报 5/5，实际 5/31） |

---

## 八、关联条目

- 主文：[deepseek-v4](./deepseek-v4.md)
- 注意力架构考据：[deepseek-v4-dsa-clarification](./deepseek-v4-dsa-clarification.md)
- 接入指南：[deepseek-v4-claude-code-integration](./deepseek-v4-claude-code-integration.md)
- 验证流程：[SOP_MULTI_AGENT_VERIFICATION_v1](../../karpathy-kb-research/research/SOP_MULTI_AGENT_VERIFICATION_v1.md)
