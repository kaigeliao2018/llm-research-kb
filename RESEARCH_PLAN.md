# LLM Research KB — 研究计划

> 定位：工程决策支撑，不是学术综述。每项结论回答「用哪个模型、为什么、多少钱」。
> 立项：2026-04-21

---

## 一、研究对象

### 第一梯队（立即跟进）

| 模型/平台 | 优先级 | 原因 |
|----------|--------|------|
| 智谱 GLM-5.1 / GLM-4.7 | 🔴 高 | 已有文档，Coding Plan 可直接替换 Claude |
| Anthropic Claude（Sonnet/Opus/Haiku） | 🔴 高 | 现有主力，基准线 |
| Google Gemini 2.0 / 2.5 | 🟡 中 | 多模态强，pegatron 视觉项目候选 |
| OpenAI GPT-4o / o3 | 🟡 中 | 行业基准，工具生态最成熟 |

### 第二梯队（按需跟进）

| 模型/平台 | 方向 |
|----------|------|
| DeepSeek-V3 / R1 | 推理强，成本极低，编程能力出色 |
| Qwen2.5 / QwQ | 阿里出品，中文场景优化 |
| Mistral / Llama 3.3 | 开源/本地部署候选 |
| Groq | 已在用（Whisper），推理速度极快 |

---

## 二、研究维度

每个模型沿以下维度评估，输出标准化 wiki 页：

1. **能力评估** → 编码 / 推理 / 视觉 / 中文 / 长上下文
2. **工具链兼容** → Claude Code / OpenClaw / Cline / API 直调
3. **成本分析** → 每百万 token 价格，有无免费额度
4. **接入难度** → 配置步骤数，坑点记录
5. **实测记录** → 真实任务跑一遍，截图/日志留档

---

## 三、研究方法

**「三步走」原则**（karpathy 风格）：

```
原始资源 (resources/raw/)
    ↓ 阅读 + 实测
加工知识 (wiki/)
    ↓ 横向对比
决策结论 (wiki/comparisons/)
```

- 官方文档 → 存 `resources/raw/`，不加工
- 实测记录 → 写进 `dev_log.md`
- 结论沉淀 → 写进 `wiki/`，加 YAML frontmatter
- 对比文章 → `wiki/comparisons/`，直接给出选型建议

---

## 四、对现有项目的增益

| 现有项目 | 当前痛点 | 研究成果如何帮助 |
|---------|---------|----------------|
| **vexiq-ai-coder** | Claude API 成本，AI 视觉传感器待做 | GLM 接入方案省钱；Gemini Vision 评估做视觉传感器 |
| **pegatron-rma-project** | 视觉自动化模型选型未定 | 多模态模型横向对比 → 直接给出选型结论 |
| **ai-teaching-kb** | 课程内容生成依赖 Claude | 评估更低成本模型替代非核心生成任务 |
| **vex-iq-kb** | 新赛季规则解析（2026-04-30） | 测试 GLM / DeepSeek 能否替代 Claude 做文档解析 |
| **所有项目** | Claude Code 成本 | GLM Coding Plan 可行性验证 → 直接降低日常开发成本 |

---

## 五、任务队列

### P0 — GLM Coding Plan 可行性实测
- 真正接入跑一下，记录成功率、速度、踩坑
- 产出：`wiki/tools/claude-code-glm-integration.md`

### P1 — GLM vs Claude 编码能力对比
- 用同一批真实任务测两个模型
- 产出：`wiki/comparisons/glm-vs-claude-coding.md`

### P2 — 多模态模型评估（for pegatron）
- Gemini / GPT-4o / GLM-4V 视觉能力实测
- 产出：`wiki/comparisons/multimodal-for-vision-automation.md`

### P3 — 成本地图
- 所有主流模型定价整理成一张表
- 产出：`wiki/cost-map.md`

---

## 六、知识管理（Obsidian）

- Obsidian vault 指向 `~/llm-research-kb/`
- `INDEX.md` 作为 Home note
- wiki 内部用 `[[wikilink]]` 建立关联
- YAML frontmatter 标记 `tags: [model, tool, comparison]`

---

## 七、下一步

**2026-04-22**：Obsidian 接入 + P0 GLM 实测开始
