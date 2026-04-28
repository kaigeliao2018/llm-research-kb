# Gemini 回执 · DeepSeek V4 Task 2+3

**收回时间**：2026-04-27
**派单方式**：网页版 Gemini，同 prompt 与 GPT 并行下发
**完整原文如下（未做任何修改）**：

---

你好，凯戈。针对你提出的 DeepSeek V4 定价验证和 Claude Code 接入这两个并行任务，我查阅了 DeepSeek 官方 API 文档（2026-04-27 最新版）、Simon Willison 的博客以及 Anthropic 开发者社区的最新反馈，整理如下：

---

### 【Task 2】DeepSeek V4 定价交叉验证

#### 1. 价格对比表 (每百万 Tokens / 1M tokens)

| 模型版本 | 输入 (Cache Miss) | 输入 (Cache Hit) | 输出价格 | 上下文窗口 | 状态/备注 |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **V3 / V3.2** | $0.27 | $0.07 | $1.10 | 128K | 当前 `deepseek-chat` 别名价格 |
| **V4-Flash** | **$0.14** | **$0.028** | **$0.28** | 1M | 极速版，对标 Haiku 4.5 |
| **V4-Pro** | **$1.74** | **$0.145** | **$3.48** | 1M | 旗舰版，对标 GPT-5.5 / Opus 4.7 |
| **V4-Pro (限时)** | $0.44 | $0.036 | $0.87 | 1M | **75% 折扣期** (至 2026-05-05) |

#### 2. 核心结论
* **对 Simon Willison 数字的结论：** **准确 (Accurate)**。Simon 给出的是 V4 正式发布后的标准价格（非折扣价）。
* **Cache Hit 优惠：** V4 延续并强化了缓存折扣。V4-Flash 的缓存命中价格仅为 $0.028/M，而 V4-Pro 的缓存命中折扣高达 **92%**，这对于 Claude Code 这种频繁发送长上下文的工具非常关键。
* **老模型下线政策 (2026-07-24)：**
    * `deepseek-chat` 和 `deepseek-reasoner` 两个 ID 将于 **2026 年 7 月 24 日** 正式停用。
    * **过渡期表现：** 目前这两个 ID 已在后台自动映射到 `deepseek-v4-flash`。
    * **建议：** 建议你在 7 月前将生产环境的 ID 显式更改为 `deepseek-v4-pro` 或 `deepseek-v4-flash`。

---

### 【Task 3】Claude Code × DeepSeek V4 接入指南

DeepSeek V4 新增了原生 **Anthropic 协议适配端点**，这使得接入 Claude Code 的复杂度大幅降低，不再需要复杂的 Proxy。

#### 1. 操作步骤 SOP

**第一步：获取 API Key**
在 [DeepSeek Platform](https://platform.deepseek.com) 开启 V4 模型权限并创建 Key。

**第二步：配置环境变量 (推荐方式)**
在你的 Mac 终端（或 `.zshrc` / `.bash_profile`）中添加：
```bash
export ANTHROPIC_BASE_URL=https://api.deepseek.com/anthropic
export ANTHROPIC_AUTH_TOKEN=sk-your-deepseek-key
export ANTHROPIC_MODEL=deepseek-v4-pro
export CLAUDE_CODE_SUBAGENT_MODEL=deepseek-v4-flash
```

**第三步：本地配置文件修改**
如果你不想用环境变量，可以直接修改 Claude Code 的设置文件：
* **全局设置：** `~/.claude/settings.json`
* **项目设置：** `.claude/settings.local.json` (该文件会被 git 忽略，适合存私密 Key)

在 `settings.local.json` 中加入：
```json
{
  "env": {
    "ANTHROPIC_BASE_URL": "https://api.deepseek.com/anthropic",
    "ANTHROPIC_AUTH_TOKEN": "sk-your-deepseek-key",
    "ANTHROPIC_MODEL": "deepseek-v4-pro"
  }
}
```

#### 2. 与 GLM (智谱) 接入的差异点
| 特性 | DeepSeek V4 接入 | GLM 接入 (凯戈经验) |
| :--- | :--- | :--- |
| **端点适配** | **原生适配**。使用 `/anthropic` 路径，完美模拟 Claude 协议。 | 通常需要中转层或特定的 OpenRouter 映射。 |
| **工具调用** | **XML 格式**。DeepSeek V4 原生支持 XML Tool Use。 | 传统上偏向 JSON 格式，在 Claude Code 中易报错。 |
| **思维链** | 返回 `reasoning_content`，需客户端透传。 | 思维过程通常在内容字段内或单独通过 API 获取。 |
| **计费透明度** | 直接支持 Anthropic 的 `usage` 字段报告。 | 计量单位映射有时存在偏差。 |

#### 3. 已知 Gotchas (避坑指南)
1.  **XML Tool Call 漂移：** 尽管 V4 优化了 XML 格式，但在执行 `bash` 命令等复杂任务时，偶发性会出现标签闭合不严的情况。如果 Claude Code 提示 `Invalid Tool Call`，建议重启 session。
2.  **Reasoning Token 计费：** `deepseek-v4-pro` 的思维过程（Thought）会计入输出 Token 计费。在 `CLAUDE.md` 中建议设置 `thinking_max_tokens: 4096` 以防费用失控。
3.  **400 Error：** 如果你的代码中没有正确处理并回传上一轮的 `reasoning_content`，DeepSeek API 会返回 400 错误。
4.  **Base URL 路径：** 务必带上后缀 `/anthropic`，否则会被识别为标准 OpenAI 协议而导致协议握手失败。


通过这套 SOP，你应该能无缝将目前的 Obsidian 知识库工作流迁移到性价比更高的 DeepSeek V4 上。如果有具体配置报错，可以随时发给我。
