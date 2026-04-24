# WIP · llm-research-kb

> 断点续传文件 · 凯戈出门前快照
> 最后更新：2026-04-24 晚（Task 1 完成后，真出门前）

---

## 当前推进的主线

**DeepSeek V4 深度研究**（发布当日切入）

- 产出主文件：`wiki/deepseek-v4.md`（已写完 11 节）
- 状态：**一稿完成，等三件事核实后进入修订阶段**

---

## 回来第一件事 · 3 个选项

**A 选项（推荐）**：派 Task 2 + Task 3 给网页版（并行）
- 两个都是纯查官网的短活，可以同一轮下发
- Prompt 在本文档"待下发 prompt 备份（Task 2 + Task 3）"节

**B 选项**：先决定是否派 Fello AI 考古
- 网页版主动提议："要不要查 Fello AI 那篇确认 'CSA+HCA' 二手来源？"
- Mac 端建议：**不派**（反转已确认，查出来不会改变结论）
- 凯戈定夺

**C 选项**：直接换主线
- DeepSeek V4 的核心已成稿，剩下都是微调
- 可切 GLM-5.1（补完 `resources/raw/` 半成品）或 Kimi K2.6

---

## 三件待核实事项（按执行顺序）

### Task 1 · DSA vs CSA+HCA 术语考据 ✅ 已完成（2026-04-24 晚）
- 交付物：`wiki/deepseek-v4-dsa-clarification.md` 已入库
- 关键反转：CSA/HCA 是官方术语（不是第三方重命名），DSA 是 V3.2 旧技术，被 CSA 包裹
- `wiki/deepseek-v4.md` 第 3.1 节已重写修正
- PDF 原件已入 `resources/raw/`
- **遗留**：网页版提议查 Fello AI 那篇确认"CSA+HCA"二手来源——凯戈决定是否派活

### Task 2 · Pricing 交叉验证 ⚪ 待派
- 派给：网页版（纯查官网）
- 和 Task 3 可**并行**
- 交付物：`wiki/deepseek-v4.md` 第六节数字确认 + 来源补齐

### Task 3 · Claude Code × DeepSeek V4 接入指南 ⚪ 待派
- 派给：网页版（查官方文档）
- 交付物：`wiki/deepseek-v4-claude-code-integration.md`

---

## 待下发 prompt 备份（Task 2 + Task 3 给网页版，可同一轮下发）

```
Task 2：DeepSeek V4 定价交叉验证

背景：Simon Willison 2026-04-24 博客给出的价格是：
- V4-Flash: $0.14 / $0.28 per M tokens（输入/输出）
- V4-Pro: $1.74 / $3.48 per M tokens
但 Fello AI 同日综述说"官方 pricing 尚未公布"。需要交叉验证。

请查：
1. DeepSeek 官方 pricing 页（api-docs.deepseek.com / platform.deepseek.com）
2. 对比 Simon 的数字
3. 是否有 cache hit 价（DeepSeek 以前的定价结构有 cache hit 折扣）
4. 旧模型 deepseek-chat / deepseek-reasoner 目前的价格（对比用）
5. 2026-07-24 老模型下线之后的过渡政策

输出：
- 一张价格对比表（V3 / V3.2 / V4-Flash / V4-Pro）
- 对 Simon 数字的结论：准确 / 过时 / 错误
- Markdown 格式，可直接入库为 wiki/deepseek-v4.md 第六节的更新，或单独 wiki/deepseek-v4-pricing.md
```

```
Task 3：Claude Code × DeepSeek V4 接入指南

背景：DeepSeek V4 官方公告明确"已集成 Claude Code"，需要落地成 SOP。

请查：
1. DeepSeek 官方集成文档（api-docs.deepseek.com 里的 Claude Code 章节）
2. Anthropic 侧（docs.claude.com/claude-code 的 third-party model 配置）
3. 具体步骤：base_url、API key 获取、配置文件改哪里（settings.json?）
4. 是否需要 proxy / adapter
5. 计费：DeepSeek 如何报告 usage 给 Claude Code（Anthropic 协议映射）
6. 社区踩坑：GitHub issues / Reddit / 知乎有没有报告的问题

对比参考（凯戈已有 GLM 接入经验）：
- https://docs.bigmodel.cn/cn/coding-plan/tool/claude
- 本地文件：resources/raw/zhipu-claude-code-integration.md

输出：
- 操作步骤 SOP（可让凯戈照着做）
- 与 GLM 接入的差异点
- 已知的 gotcha
- Markdown 格式，可直接入库为 wiki/deepseek-v4-claude-code-integration.md
```

---

## 后续线路图（Task 1 完成后）

**本主线收尾（DeepSeek V4）**：
- Task 2 + Task 3 并行下发网页版
- 根据 3 个 Task 回执修订 `wiki/deepseek-v4.md` v2
- 打 git tag，入库完成

**下一主线候选（凯戈批过优先级）**：
1. 🥇 **GLM-5.1 补完** — `resources/raw/` 已有 2 份半成品，整合为 `wiki/glm-5.1.md`，ROI 最高
2. 🥈 **Kimi K2.6** — 中文评测密集，适合网页版外勤
3. 之后排：Qwen、Gemini 3.1 Pro、GPT-5.4、Claude 4.5/4.6

---

## 未决事项（回来可讨论）

- `RESEARCH_PLAN.md` 还是空的，要不要补一版正式规划？
- KB 内部命名规范（文件命名 / frontmatter 字段）是否需要先定 SOP？
- 是否需要建立"模型速查表"总索引页？（`wiki/INDEX.md` 或 `wiki/frontier-models-comparison.md`）

---

## 文件索引（本次新增/修改）

| 文件 | 状态 | 说明 |
|------|------|------|
| `wiki/deepseek-v4.md` | 新建 | 11 节完整解读，待核实 3 处 |
| `resources/links.md` | 更新 | 新增 DeepSeek 分区，6 条链接 |
| `dev_log.md` | 更新 | 加 2026-04-24 两段条目 |
| `WIP.md` | 新建 | 本文件 |
