# DeepSeek V4 深度解读

> 研究日期：2026-04-24（模型发布当天）
> 状态：Preview 版本刚发布，信息基于官方公告+第三方评测交叉核对
> 研究员：凯戈 + Claude

---

## 一、发布概览

| 项 | 内容 |
|----|------|
| 发布日期 | 2026-04-24（Preview） |
| 开源仓库 | [deepseek-ai/DeepSeek-V4-Pro](https://huggingface.co/deepseek-ai/DeepSeek-V4-Pro) · [DeepSeek-V4-Flash](https://huggingface.co/deepseek-ai/DeepSeek-V4-Flash) |
| License | MIT（权重开源，商用友好） |
| 技术报告 | [DeepSeek_V4.pdf](https://huggingface.co/deepseek-ai/DeepSeek-V4-Pro/blob/main/DeepSeek_V4.pdf)（HF 仓库内） |
| 官方公告 | [api-docs.deepseek.com/news/news260424](https://api-docs.deepseek.com/news/news260424) |
| 旧模型退役 | `deepseek-chat` / `deepseek-reasoner` 将于 2026-07-24 下线 |

---

## 二、模型变体

DeepSeek V4 首发两个 MoE 变体，形成"旗舰+经济"双层产品线：

| 变体 | 总参数 | 激活参数 | 权重大小 | 定位 |
|------|--------|---------|---------|------|
| **V4-Pro** | 1.6 T | 49 B | ~865 GB | 旗舰，对标闭源前沿模型 |
| **V4-Flash** | 284 B | 13 B | ~160 GB | 速度/成本优化，简单 agent 任务接近 Pro |

横向对比（同期 MoE 开源模型）：
- Kimi K2.6：1.1 T
- GLM-5.1：754 B
- DeepSeek V3.2：685 B
- **V4-Pro 是 V3.2 总参数的 2 倍以上**

两款均内置 **thinking / non-thinking 双模式**（类似 Claude Sonnet 的 extended thinking）。
V4-Pro 的最高推理档位叫 **V4-Pro-Max**（thinking 全开），benchmark 分数以这个档位为准。

---

## 三、架构核心创新

### 3.1 混合注意力机制（Hybrid Attention）

> ✅ 2026-04-24 已核对技术报告 PDF（详见 [deepseek-v4-dsa-clarification.md](./deepseek-v4-dsa-clarification.md)）

V4 真正的架构创新是 **CSA 和 HCA 两种注意力层的交替堆叠**（hybrid interleaved），两者都是 V4 论文在 Abstract 里就提出的**官方术语**。

- **CSA（Compressed Sparse Attention）** —— V4 新创
  1. 将每 m 个 token 的 KV cache 压缩为一条
  2. 在压缩后的 KV 上运行 **DSA（DeepSeek Sparse Attention）**，每个 query 只 attend to top-k 压缩项
  3. DSA 本身是 V3.2 的旧技术，V4 沿用，作为 CSA 内部的第二步

- **HCA（Heavily Compressed Attention）** —— V4 新创
  1. 以更激进的压缩率（m' ≫ m）压缩 KV cache
  2. 在压缩后的 KV 上做 **dense attention**（不稀疏）

**三者关系速记**：`CSA = KV 压缩 + DSA`；`HCA = 更狠的 KV 压缩 + 稠密 attention`；`DSA ⊂ CSA`（旧技术，被包在外层里）。

首轮常见误解：把 DSA 写成 V4 的核心创新。**实际上 V4 的创新点在 CSA+HCA 的 hybrid 组合**，以及 HCA 这条"只压不稀疏"的新路径。

### 3.2 训练侧改动
- **优化器**：主参数从 AdamW 换成 **Muon**
- **精度**：专家权重使用 **FP4 量化训练**（成本压缩关键）
- **稳定性补丁**：引入 **Anticipatory Routing**（前瞻式路由，防 expert 争用）+ **SwiGLU Clamping**（抑制激活爆炸）

### 3.3 长上下文
- **原生 1M token context**（与 Kimi K2、Gemini 并肩）
- **⚠️ 实测注意**：Fello AI 报告 128K 以上长上下文检索质量下降——Simon Willison 也未否认——不是"1M 都能用"，超过 128K 要谨慎

---

## 四、效率收益（相比 V3.2）

这是本次发布最关键的商业价值点：**同样能力，成本断崖式下降**。

| 指标 | V4-Pro vs V3.2 | V4-Flash vs V3.2 |
|------|---------------|------------------|
| 单 token 推理 FLOPs | 27% | 10% |
| KV Cache 体积 | 10% | 7% |

翻译成人话：
- Pro 在 1M context 下，算力消耗降到 V3.2 的 **1/4**，显存占用降到 **1/10**
- Flash 进一步压缩到 **1/10 算力**、**~1/14 显存**

这是为什么价格可以打到下面这种水平。

---

## 五、性能表现（V4-Pro-Max）

**编程领域表现最强：**
- **Codeforces Rating：3,206**（在真人选手中排名约第 23 位）
- **LiveCodeBench：93.5%**
- 在"竞赛编程 + 形式化数学"上**领先 Gemini 3.1 Pro**

**知识/通识领域小幅落后：**
- SimpleQA-Verified：57.9%
- MMLU-Pro、SimpleQA 上不及 Gemini 3.1 Pro

**Simon Willison 的定性评价**：
> "与其他前沿模型有竞争力，但在推理任务上略逊 GPT-5.4 和 Gemini-3.1-Pro，发展轨迹落后最先进模型约 3–6 个月。"

---

## 六、价格

来源：Simon Willison 2026-04-24 评测（官方 pricing 页面尚未统一公开，Fello AI 称"未公布"——取 Simon 的数字并标注）

| 模型 | 输入（$/M tok） | 输出（$/M tok） |
|------|---------------|---------------|
| V4-Flash | **$0.14** | **$0.28** |
| V4-Pro | **$1.74** | **$3.48** |

**对比坐标**：
- V4-Flash 比 GPT-5.4 Nano 还便宜——**当前最便宜的小型前沿模型**
- V4-Pro 是大型前沿模型中**最便宜的一档**

---

## 七、接入方式

### API（零迁移成本）
```
base_url 不变
model 改为：deepseek-v4-pro 或 deepseek-v4-flash
```
- 同时兼容 **OpenAI ChatCompletions** 和 **Anthropic Messages** 两种协议
- 旧名 `deepseek-chat` / `deepseek-reasoner` 2026-07-24 下线，需提前迁移

### 客户端集成
官方明确列出已集成：
- **Claude Code**
- **OpenClaw**
- **OpenCode**

→ 对凯戈意味着：Claude Code 可以直接把模型切成 deepseek-v4，和之前接 GLM 同路子。

### 自托管
- **V4-Flash**：单机高配服务器可跑（160GB 权重，估计 2×H100 / 4×A100 起步）
- **V4-Pro**：865GB 权重，需要集群级基础设施（多节点 H100/H200 规模）
- 网页免费体验：[chat.deepseek.com](https://chat.deepseek.com)

---

## 八、局限性（已明确）

1. **纯文本模型**——无多模态（无视觉、无音频）
2. **知识广度不及 Gemini 3.1 Pro**——MMLU-Pro / SimpleQA 存在差距
3. **长上下文 >128K 检索衰减**——1M 是"能装下"，不是"全能用好"
4. **延迟**：thinking 模式响应慢（行业通病，不是 V4 独有）

---

## 九、对凯戈项目的潜在价值

> 此节为本地决策，非第三方信息

| 项目 | 匹配度 | 用法 |
|------|--------|------|
| **Claude Code 主力编程** | 🟡 | V4-Pro 编程能力强 + 超低价，可作 Claude Sonnet 的备胎／廉价路线 |
| **vexiq-ai-coder** | 🟢 | 编程能力强 + 价格低，适合调用给学生机器人代码生成 |
| **ai-teaching-kb 总结管线** | 🟢 | 1M context + $0.14/M 输入 = 超便宜长文处理 |
| **karpathy-kb-research PDF 解析后整理** | 🟢 | 长文档问答超便宜 |
| **llm-research-kb 自身** | 🟢 | 后续对比评测可纳入基线 |

**建议动作**：
1. 先用网页版（chat.deepseek.com）感性体验 thinking / non-thinking 差异
2. Claude Code 可考虑挂 V4-Pro 做 A/B（成本对比：Claude Sonnet 输入 $3/M vs V4-Pro $1.74/M）
3. 不建议自托管——860GB 是商业/学术基础设施才承担得起的

---

## 十、待核实事项（TODO）

- [x] ~~核对 DSA 与 CSA+HCA 关系~~ ✅ 2026-04-24 已核（[考据笔记](./deepseek-v4-dsa-clarification.md)）
- [ ] 官方 pricing 页面何时正式公开，Simon 的数字是否准确（Task 2 待派）
- [ ] Claude Code 实际接入测试（Task 3 待派）
- [ ] MoE 路由效果实测，V4-Flash 在凯戈实际场景下能否替代 V4-Pro
- [ ] Hybrid Attention 详细实现（CSA/HCA 交替比例、每模型层数）—— 见 PDF 4.2.1 节（第 25 页）

---

## 十一、参考资料

**一手资料：**
- 官方公告：https://api-docs.deepseek.com/news/news260424
- V4-Pro 模型卡：https://huggingface.co/deepseek-ai/DeepSeek-V4-Pro
- V4-Flash 模型卡：https://huggingface.co/deepseek-ai/DeepSeek-V4-Flash
- 技术报告 PDF：https://huggingface.co/deepseek-ai/DeepSeek-V4-Pro/blob/main/DeepSeek_V4.pdf
- 社区镜像（unsloth）：https://huggingface.co/unsloth/DeepSeek-V4-Pro

**独立评测/综述：**
- Simon Willison（2026-04-24）：https://simonwillison.net/2026/Apr/24/deepseek-v4/
- Fello AI 发布综述：https://felloai.com/deepseek-v4/
- TokenMix 发布跟踪（含前期延期传闻）：https://tokenmix.ai/blog/deepseek-v4-release-delay-huawei-chip-2026
