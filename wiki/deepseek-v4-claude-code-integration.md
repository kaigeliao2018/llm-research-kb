---
name: deepseek-v4-claude-code-integration
description: Claude Code 接入 DeepSeek V4 操作 SOP（官网 anthropic 端点 + 凯戈实测建议）
date: 2026-04-27
sources:
  - 官网 Anthropic 集成页: https://api-docs.deepseek.com/guides/anthropic_api
  - 官网概览: https://api-docs.deepseek.com/
  - GLM 接入参考（凯戈历史经验）: resources/raw/zhipu-claude-code-integration.md
  - 多 Agent 验证批次: resources/raw/deepseek-v4/verify-batch-001/
verification: 双 Agent 派单 (Gemini + GPT) → Claude WebFetch 终审；详见 cross-check.md
---

# Claude Code × DeepSeek V4 接入指南

> 官网信息来自 [Source: WebFetch https://api-docs.deepseek.com/guides/anthropic_api]
> 经验性 Gotcha 来自 Gemini 网页版报告，统一标 `[STRICT-VERIFY]`，凯戈实测后再升级

---

## 一、为什么 V4 接入比 GLM 简单

DeepSeek V4 在 `https://api.deepseek.com/anthropic` 提供**原生 Anthropic 协议端点**，
直接消费 Claude Code 的协议握手，**不需要中转 proxy**。
[Source: WebFetch https://api-docs.deepseek.com/guides/anthropic_api]

对比凯戈已有 GLM 接入经验（见 `resources/raw/zhipu-claude-code-integration.md`）：

| 维度 | DeepSeek V4 | GLM (智谱) |
|---|---|---|
| 协议端点 | 官方 anthropic 路径 | 通常需 OpenRouter 或自建中转 [STRICT-VERIFY] |
| 端点 URL | `https://api.deepseek.com/anthropic` | （以 GLM 文档为准） |
| 安装依赖 | `pip install anthropic` | 同 |

---

## 二、操作 SOP

### 第一步：申请 API Key

1. 注册 DeepSeek 平台账号：https://platform.deepseek.com
2. 控制台 → API Keys → 创建新 Key
3. 在控制台开启 V4 模型权限（如无此选项则默认开放）

### 第二步：配置环境变量

**官网示范**（推荐）[Source: WebFetch https://api-docs.deepseek.com/guides/anthropic_api]：

```bash
export ANTHROPIC_BASE_URL=https://api.deepseek.com/anthropic
export ANTHROPIC_API_KEY=${YOUR_DEEPSEEK_API_KEY}
```

**Gemini 报告的备选写法**（仍待凯戈实测）：

```bash
export ANTHROPIC_BASE_URL=https://api.deepseek.com/anthropic
export ANTHROPIC_AUTH_TOKEN=${YOUR_DEEPSEEK_API_KEY}    # [STRICT-VERIFY]
export ANTHROPIC_MODEL=deepseek-v4-pro                  # [STRICT-VERIFY]
export CLAUDE_CODE_SUBAGENT_MODEL=deepseek-v4-flash     # [STRICT-VERIFY] Claude Code 自身变量，与 DeepSeek 无关，可选
```

> **[STRICT-VERIFY]**：`ANTHROPIC_AUTH_TOKEN` vs `ANTHROPIC_API_KEY` 两个变量名都见过，
> Claude Code 在不同上下文下接受程度不同。**优先按官网用 `ANTHROPIC_API_KEY`**，
> 若不生效再切 `ANTHROPIC_AUTH_TOKEN`。实测后请把结果写回本文档。

### 第三步：项目级配置（替代方案）

不愿污染全局环境时，在项目根的 `.claude/settings.local.json` 写：

```json
{
  "env": {
    "ANTHROPIC_BASE_URL": "https://api.deepseek.com/anthropic",
    "ANTHROPIC_API_KEY": "sk-your-deepseek-key"
  }
}
```

注意：`.claude/settings.local.json` 默认在 `.gitignore` 内，不会被 commit。

### 第四步：调用模型

```python
from anthropic import Anthropic
client = Anthropic()
msg = client.messages.create(
    model="deepseek-v4-pro",   # 或 "deepseek-v4-flash"
    max_tokens=1024,
    messages=[{"role": "user", "content": "Hello"}]
)
```

**官方说明**：传入未识别的 `model` 名时，"the API backend will automatically map it to the `deepseek-v4-flash`"。
[Source: WebFetch https://api-docs.deepseek.com/guides/anthropic_api]

---

## 三、模型选择建议

| 场景 | 推荐 | 理由 |
|---|---|---|
| Claude Code 主对话 | `deepseek-v4-pro` | 编程能力强，对标 Sonnet 级 |
| 子代理 / Explore / 大量小任务 | `deepseek-v4-flash` | 价格便宜 12 倍，简单任务够用 |
| 长上下文重复调用（KB 问答） | 任意，但开 cache | Cache hit 99% 折扣，2026-04-26 起生效 |

定价细节见 [deepseek-v4-pricing](./deepseek-v4-pricing.md)。

---

## 四、已知踩坑（[STRICT-VERIFY]，未在官网确认）

> 以下条目来自 Gemini 网页版 2026-04-27 报告，**未在 DeepSeek 官网或 Claude Code 官方 docs 找到对应章节**。
> 保留备查，凯戈实测命中再去掉 STRICT-VERIFY 标记。

1. **XML Tool Call 闭合不严**：在执行 `bash` 命令等复杂工具调用时，可能出现 `Invalid Tool Call` 报错；建议重启 session [STRICT-VERIFY]
2. **Reasoning Token 计费**：`deepseek-v4-pro` 思考过程会计入输出 Token；建议在 CLAUDE.md 设 `thinking_max_tokens: 4096` 防失控 [STRICT-VERIFY]（注：`thinking_max_tokens` 字段未在 Claude Code 官方文档查到，疑为 Gemini 编造的字段名）
3. **400 Error · reasoning_content 透传**：若客户端未正确回传上一轮 `reasoning_content`，DeepSeek API 返回 400 [STRICT-VERIFY]
4. **Base URL 路径**：必须带 `/anthropic` 后缀，否则被识别为 OpenAI 协议导致握手失败 ✅（与官网文档一致）

---

## 五、与 GLM 接入差异点（速查）

| 特性 | DeepSeek V4 | GLM | 备注 |
|---|---|---|---|
| 端点适配 | 原生 `/anthropic` | 中转 / OpenRouter | DeepSeek 简单 |
| 工具调用格式 | XML（Anthropic 原生）[STRICT-VERIFY] | JSON | Gemini 报告，未官方核实 |
| 思维链返回 | `reasoning_content` 字段 [STRICT-VERIFY] | 内嵌内容字段 | 同上 |
| 计费透明度 | 直接走 Anthropic `usage` 字段 [STRICT-VERIFY] | 单位映射偶有偏差 | 同上 |

---

## 六、版本历史

| 日期 | 变更 |
|------|------|
| 2026-04-27 | 首版；官网核实部分入正文，未核实部分全标 STRICT-VERIFY 待凯戈实测 |

---

## 七、关联条目

- 主文：[deepseek-v4](./deepseek-v4.md)
- 定价表：[deepseek-v4-pricing](./deepseek-v4-pricing.md)
- 验证流程：[SOP_MULTI_AGENT_VERIFICATION_v1](../../karpathy-kb-research/research/SOP_MULTI_AGENT_VERIFICATION_v1.md)
- GLM 接入参考：`resources/raw/zhipu-claude-code-integration.md`
