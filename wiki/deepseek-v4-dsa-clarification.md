---
name: deepseek-v4-dsa-clarification
description: DeepSeek V4 注意力机制术语考据——DSA、CSA、HCA 三者关系
date: 2026-04-24
sources:
  - DeepSeek V4 技术报告 PDF（本地: resources/raw/DeepSeek_V4.pdf）
  - Simon Willison 2026-04-24 博客 PDF（本地: resources/raw/DeepSeek_V4_almost_on_the_frontier.pdf）
  - Simon Willison 原文: https://simonwillison.net/2026/Apr/24/deepseek-v4/
  - HuggingFace 模型页: https://huggingface.co/deepseek-ai/DeepSeek-V4-Pro
---

# DeepSeek V4 注意力机制：DSA / CSA / HCA 术语考据

## 一、素材清单（先对账后下结论）

**素材 A**：DeepSeek V4 官方技术报告 PDF，58 页
**素材 B**：Simon Willison 2026-04-24 博客 "DeepSeek V4—almost on the frontier, a fraction of the price"，5 页

## 二、逐条引文核对

### 引文 1：Abstract（官方 PDF 第 1 页）

> "DeepSeek-V4 series incorporate several key upgrades in architecture and optimization: (1) a hybrid attention architecture that combines **Compressed Sparse Attention (CSA)** and **Heavily Compressed Attention (HCA)** to improve long-context efficiency"

→ **CSA 和 HCA 是 V4 官方在摘要里首句就提出的正式术语**，不是第三方重命名。

### 引文 2：Introduction（官方 PDF 第 4 页）

> "To enhance long-context efficiency, we design a hybrid attention mechanism combining Compressed Sparse Attention (CSA) and Heavily Compressed Attention (HCA). **CSA compresses the KV caches along the sequence dimension and then performs DeepSeek Sparse Attention (DSA) (DeepSeek-AI, 2025)**, whereas HCA applies more aggressive compression to the KV caches but keeps dense attention."

→ 这一段明确了三者的层级关系：
- **DSA** 是 V3.2（DeepSeek-AI, 2025 引用，指 V3.2 论文）就引入的旧技术
- **CSA** 是 V4 新设计的外层——先压缩 KV，再**内部调用** DSA
- **HCA** 是 V4 新设计的另一种注意力，**不使用** DSA，只做更激进的压缩 + dense attention

### 引文 3：第 2.3 节（官方 PDF 第 9 页）

> "For DeepSeek-V4, we design two efficient attention architectures — Compressed Sparse Attention (CSA) and Heavily Compressed Attention (HCA) — and employ their interleaved hybrid configuration"

> "CSA integrates both compression and sparse attention strategies: it first compresses the Key-Value (KV) cache of every *m* tokens into one entry, and then **applies DeepSeek Sparse Attention (DSA) (DeepSeek-AI, 2025)** where each query token attends to only *k* compressed KV entries."

→ 再次确认：CSA 的第二步 = DSA。DSA 是 CSA 的**组成部分**，不是平级关系。

### 引文 4：模型架构图（官方 PDF 第 6 页，Figure 2）

Figure 2 的 Transformer Block 里标注的是 `CSA / HCA`，**图中没有出现 DSA 这个缩写**——因为 DSA 被包在 CSA 内部，在更细的 Figure 3（第 9 页）里才展开。

### 引文 5：Simon Willison 博客（素材 B，全文）

通读 5 页，**Simon 的文章里完全没有出现 "CSA"、"HCA"、"DSA" 三个术语中的任何一个**。他只从使用者角度讲了三件事：模型规模（1.6T / 284B）、价格对比表、pelican SVG 测试。唯一引用的 paper 原文是关于 FLOPs / KV cache 效率的那段（素材 B 第 4 页），没触及注意力架构命名。

## 三、结论

**三者关系**：

```
CSA（V4 新术语，官方）       HCA（V4 新术语，官方）
 │                          │
 ├── 步骤1: 压缩 KV cache     ├── 步骤1: 更激进地压缩 KV cache（m' ≫ m）
 └── 步骤2: 调用 DSA          └── 步骤2: dense attention（不用 DSA）
         │
         └── DSA（V3.2 旧术语，官方）
             = DeepSeek Sparse Attention
             = top-k 稀疏选择 + MQA
```

**术语归属**：

| 术语 | 首次定义来源 | 性质 |
|-----|-------------|------|
| DSA | DeepSeek V3.2 论文（2025） | 官方术语，V4 沿用 |
| CSA | DeepSeek V4 论文（本次 PDF） | V4 新创官方术语 |
| HCA | DeepSeek V4 论文（本次 PDF） | V4 新创官方术语 |

**凯戈 dev_log 里原问题的修正**：

dev_log 原话是"DeepSeek V4 技术报告 PDF 中 'DSA' 的具体定义，与第三方评测说的 'CSA + HCA' 如何对应"——这个理解把归属倒置了。实际是：
- CSA / HCA **本身就是官方术语**，不是第三方重命名
- 如果某篇中文/英文二手评测用 "CSA+HCA" 介绍 V4 的注意力架构，它是在**复述官方说法**
- V4 架构 = 交替堆叠 CSA 和 HCA 层；CSA 内部复用 V3.2 的 DSA

**wiki/deepseek-v4.md 需要修正的表述建议**：
- 不要把 DSA 写成 V4 的核心注意力创新——DSA 是 V3.2 的旧技术
- V4 的真正架构创新是 CSA + HCA 的 hybrid 组合，以及 HCA 的 dense 路径
- 如果原文写了"DSA 压缩 + 稀疏选择"之类的一体化描述，应当拆成"CSA = KV 压缩 → DSA 稀疏选择"的两步

## 四、存疑与未核实项

- ⚠️ **未核实**：凯戈提到的"第三方评测"具体是哪一篇（dev_log 里没写出处，Simon 的 PDF 也没用这组术语）。如果指的是 Fello AI 那篇 <https://felloai.com/deepseek-v4/>，本轮按凯戈要求"优先以两份 PDF 为准"未展开查证，保留待核实。
- ⚠️ 官方 PDF 在 Hybrid Attention 的具体实现细节（比如 CSA/HCA 层的交替比例、每个模型各用几层）写在 4.2.1 节（第 25 页），本笔记未详细展开，如需补全可另起一篇。
- ✅ **已核实**：DSA 归属 V3.2，CSA/HCA 均为 V4 官方命名，三者层级关系如上图。
