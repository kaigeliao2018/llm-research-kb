---
name: kimi-k2-6 素材盘点
description: Kimi K2.6 研究素材初次盘点（Web 源），为下次开工做准备
date: 2026-04-27
inventory_type: 1B (Web/纯文本源盘点 — SOP_GENERIC_v1 §1B)
status: 素材归档完成，未开始研究
---

# Kimi K2.6 素材盘点（批次 000）

> **本文档目的**：为下次开 Kimi K2.6 主线提供素材入口与待核实清单
> **不做**：本次不展开研究，等下次按 SOP_MULTI_AGENT_VERIFICATION 流程派单

---

## 一、主要来源（5 条 URL，待 WebFetch 抓正文）

| # | 来源类型 | URL | 价值 | 备注 |
|---|---|---|---|---|
| 1 | Kimi 官方产品页 | `https://www.kimi.com/ai-models/kimi-k2-6` | 🔴 高 | 官方主张：Coding & Agent Leading Open-Source |
| 2 | HuggingFace | `https://huggingface.co/moonshotai/Kimi-K2` | 🔴 高 | 官方权重仓库，模型卡 + 技术报告 |
| 3 | 第三方博客 | `https://kimi-k2.org/blog/24-kimi-k2-6-release` | 🟡 中 | 2026-04-21 发布综述，含发布时间线 |
| 4 | GitHub repo | `https://github.com/kimi-K2-6/kimi-K2` | ⚪ 待核 | **[STRICT-VERIFY]** 仓库名命名空间 `kimi-K2-6` 可疑，非官方 MoonshotAI 命名风格，疑为 fork/fan 项目 |
| 5 | API 文档 | `https://platform.kimi.ai/docs/guide` | 🔴 高 | 官方接入文档，含定价、调用方式 |

---

## 二、核心声明清单（来自截图 snippet，未核实）

> 全部 [STRICT-VERIFY]，等下次 WebFetch 实测原文

1. K2.6 是 "Leading Open-Source Model in Coding & Agent"（来源 1）
2. 强调 "complete products, complex workflows, autonomous coordinated agents, reusable skills"（来源 1）
3. 2026-04-13 Moonshot AI 邮件确认 beta tester 在跑 K2.6 Code Preview（来源 3）
4. 2026-04-21 移除 "Preview" 标签正式发布（来源 3）
5. 官方称 "stronger and more stable long-term code writing capabilities, significantly improved instruction compliance and self-correction"（来源 5）

## 三、图片与表格

| 项 | 数量 |
|---|---|
| 图片 | 1 张（Google 搜索结果截图，存 `screenshots/google-search-2026-04-27.png`）|
| 表格 | 0 |

## 四、可入库条目提案（下次开工时落盘）

| 提案文件 | 内容 |
|---|---|
| `wiki/kimi-k2-6.md` | 主文：发布时间线 + 架构 + 性能 + 价格 + 接入 + 局限 |
| `wiki/kimi-k2-6-pricing.md` | 子页：API 定价（来源 5 抓数字） |
| `wiki/kimi-k2-6-claude-code-integration.md` | 子页：Claude Code 接入指南（若 Kimi 也提供 anthropic 端点） |

---

## 五、下次开工 SOP（按 SOP_MULTI_AGENT_VERIFICATION_v1）

### 步骤 1：派单（Gemini + GPT 双发）

**派单 prompt 模板**（套用 DeepSeek V4 验证批次的格式）：

```
你好 {Gemini/GPT}，请独立查证（不参考其他 AI 的结论）：

【任务】Kimi K2.6 深度调研（2026-04-21 发布）

请查官方信息源：
1. https://www.kimi.com/ai-models/kimi-k2-6
2. https://huggingface.co/moonshotai/Kimi-K2
3. https://platform.kimi.ai/docs/guide

输出（Markdown）：
- 发布时间线（preview → 正式发布关键节点）
- 模型规模（总参数 / 激活参数 / 上下文长度）
- 与 K2 的差异（增量在哪里）
- API 定价表（含 cache hit 折扣若有）
- Claude Code 接入方式（是否有 anthropic 端点）
- 已知局限（多模态？长上下文衰减？）
- 与 DeepSeek V4-Pro / Claude Sonnet 4.6 的对比定位
```

### 步骤 2-5：回收 → 交叉 → Claude WebFetch 核实 → 入库

详见 [SOP_MULTI_AGENT_VERIFICATION_v1](../../../../karpathy-kb-research/research/SOP_MULTI_AGENT_VERIFICATION_v1.md)

---

## 六、警示

基于 DeepSeek V4 验证批次教训：

- **Gemini 给具体数字（价格/日期/参数）时一律不直接采信**，等 WebFetch 实测
- **kimi-k2.org 域名可疑**：`.org` 后缀 + 非 Moonshot 主域名，疑为社区/SEO 站，**只能当二手参考**
- **GitHub `kimi-K2-6/kimi-K2` 仓库**：命名风格不像 MoonshotAI 官方组织，下次第一步先用 GitHub API 验证 owner 身份，可能是 fork 或冒名仓库

---

## 七、状态

- [x] 素材归档（截图 + URL 清单）
- [x] 盘点表完成
- [ ] 派单（Gemini + GPT，下次执行）
- [ ] 核实（Claude WebFetch）
- [ ] 入库（wiki/kimi-k2-6*.md）
