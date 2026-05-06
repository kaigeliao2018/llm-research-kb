# GPT-5.5

> 研究日期：2026-05-07
> 状态：公开发布；信息基于 deploymentsafety.openai.com（官方）+ TechCrunch/CNBC（新闻）+ llm-stats（三方评测站）
> 研究员：凯戈 + Claude
> 注意：openai.com 主发布页及 System Card 返回 403，能力 benchmark 官方原文未抓取

---

## 一、发布概览

| 项 | 内容 |
|----|------|
| 发布日期 | 2026-04-23（GPT-5.5 主体）|
| 追加发布 | 2026-05-05（GPT-5.5 Instant，ChatGPT 新默认模型）|
| 前代模型 | GPT-5.4 / GPT-5.4-thinking |
| 定性 | OpenAI 首个完整重训基础模型（自 GPT-4.5 以来）|
| 官方文档 | [Introducing GPT-5.5](https://openai.com/index/introducing-gpt-5-5/)（403，未抓取）· [GPT-5.5 System Card](https://openai.com/index/gpt-5-5-system-card/)（403，未抓取）· [Deployment Safety Hub](https://deploymentsafety.openai.com/gpt-5-5)（✅ 已抓取）|
| 新闻来源 | [TechCrunch 发布报道](https://techcrunch.com/2026/04/23/openai-chatgpt-gpt-5-5-ai-model-superapp/) · [TechCrunch GPT-5.5 Instant](https://techcrunch.com/2026/05/05/openai-releases-gpt-5-5-instant-a-new-default-model-for-chatgpt/) · [CNBC](https://www.cnbc.com/2026/04/23/openai-announces-latest-artificial-intelligence-model.html) |

---

## 二、架构与规格

> 来源：TechCrunch / 搜索摘要，**可靠新闻，非官方原文**

- **原生全模态**：文本、图像、音频、视频统一单一模型处理（非多模型拼接）
- **上下文窗口**：1M token（API）/ 400K token（Codex）
- **Token 效率**：比 GPT-5.4 少用约 40% output token
- **可用范围**：Plus、Pro、Business、Enterprise 用户（ChatGPT + Codex）

---

## 三、能力 Benchmark

### 3.1 安全评估（官方数据）

> 来源：deploymentsafety.openai.com ✅ **官方，已直接抓取**

**生产安全 Benchmark（Table 1，越高越好）：**

| 类别 | GPT-5.5 | GPT-5.4-thinking |
|------|---------|-----------------|
| Violent Illicit | **0.979** | 0.971 |
| Harassment | **0.822** | 0.790 |
| Hate | 0.868 | **0.943** |

**健康类 Benchmark（Table 7，length-adjusted）：**

| 项目 | GPT-5.5 | GPT-5.4 |
|------|---------|---------|
| HealthBench | **56.5** | 54.0 |
| HealthBench Hard | **31.5** | 29.1 |
| HealthBench Professional | **51.8%** | 48.1% |

**网络安全（Table 15）：**

| 项目 | GPT-5.5 | GPT-5.4-thinking |
|------|---------|-----------------|
| Cyber Range 综合通过率 | **93.33%** | 73.33% |

### 3.2 综合能力 Benchmark（三方数据）

> 来源：llm-stats.com ⚠️ **三方评测聚合站，非官方；官方 System Card 因 403 未抓取**

| Benchmark | GPT-5.5 |
|-----------|---------|
| ARC-AGI v2 | 85.0%（比 GPT-5.4 的 73.3% 跳升 11.7 点）|
| GPQA | 93.6% |
| MMMU-Pro | 83.2% |
| Terminal-Bench 2.0 | 82.7% |
| OSWorld-Verified | 78.7% |
| BrowseComp | 84.4% |
| CyberGym | 81.8% |
| MCP Atlas | 75.3% |
| SWE-Bench Pro | 58.6% |
| Humanity's Last Exam | 52.2% |
| FrontierMath | 35.4% |
| Tau2 Telecom | 98.0% |
| Toolathlon | 55.6% |

---

## 四、价格

> 来源：评测对比文章（三方）⚠️

| 项目 | GPT-5.5 |
|------|---------|
| Input | $5.00 / 1M tokens |
| Output | $30.00 / 1M tokens |

---

## 五、与 Claude Mythos Preview 对比

> 注意：这是**不对等比较**——GPT-5.5 是公开旗舰，Mythos Preview 是限量安全研究模型（非公开）
> 来源：llm-stats.com ⚠️ 三方

| Benchmark | GPT-5.5 | Claude Mythos Preview |
|-----------|---------|----------------------|
| SWE-Bench Pro | 58.6% | **77.8%** |
| HLE | 52.2% | **64.7%** |
| GPQA | 93.6% | **94.6%** |
| OSWorld | 78.7% | **79.6%** |
| BrowseComp | 84.4% | **86.9%** |
| Terminal-Bench 2.0 | **82.7%** | 82.0% |
| ARC-AGI v2 | **85.0%** | N/A |

Mythos Preview 在推理 / 代码类大幅领先，GPT-5.5 在 agentic 执行类（Terminal-Bench / ARC-AGI）持平或略强。

---

## 六、GPT-5.5 Instant（2026-05-05）

> 来源：TechCrunch ✅ 可靠新闻

- ChatGPT 新默认模型（替换旧默认）
- 新增：可调用 search 工具回溯历史对话、文件、Gmail 给出个性化回答

---

## 七、踩坑记录

| 坑 | 说明 |
|----|------|
| **openai.com 全面 403** | 主发布页 + System Card 均无法抓取，能力 benchmark 官方原文缺失 |
| **Mythos 对比不对等** | 第三方评测站拿 GPT-5.5 vs Mythos Preview 对比，后者是限量非公开模型，结论不具普适参考价值 |
| **token 效率 ≠ 实际费用更低** | 少用 40% token，但 output 单价比 GPT-5.4 高，实际成本需按场景计算 |

---

## 八、参考资料

- [GPT-5.5 Deployment Safety Hub](https://deploymentsafety.openai.com/gpt-5-5) ✅ 官方
- [Introducing GPT-5.5 | OpenAI](https://openai.com/index/introducing-gpt-5-5/) ⛔ 403
- [GPT-5.5 System Card | OpenAI](https://openai.com/index/gpt-5-5-system-card/) ⛔ 403
- [OpenAI releases GPT-5.5 | TechCrunch](https://techcrunch.com/2026/04/23/openai-chatgpt-gpt-5-5-ai-model-superapp/) ✅ 可靠新闻
- [GPT-5.5 Instant | TechCrunch](https://techcrunch.com/2026/05/05/openai-releases-gpt-5-5-instant-a-new-default-model-for-chatgpt/) ✅ 可靠新闻
- [GPT-5.5 Benchmarks | llm-stats.com](https://llm-stats.com/models/gpt-5.5) ⚠️ 三方评测站
