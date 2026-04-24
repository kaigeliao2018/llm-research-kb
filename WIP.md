# WIP · llm-research-kb

> 断点续传文件 · 凯戈出门前快照
> 最后更新：2026-04-24 下午

---

## 当前推进的主线

**DeepSeek V4 深度研究**（发布当日切入）

- 产出主文件：`wiki/deepseek-v4.md`（已写完 11 节）
- 状态：**一稿完成，等三件事核实后进入修订阶段**

---

## 回来第一件事 · 5 分钟可动作

1. 打开网页端 Claude Projects "llm-research-kb"
2. 把桌面上这两份 PDF 拖进新对话框：
   - `~/Desktop/DeepSeek_V4.pdf`
   - `~/Desktop/DeepSeek+V4—almost+on+the+frontier,+a+fraction+of+the+price.pdf`
3. 粘贴 **Task 1 prompt**（正文见下方"待下发 prompt 备份"）
4. 等网页版回复 → 把回复原样发给 Mac 端 Claude Code → 入库为 `wiki/deepseek-v4-dsa-clarification.md`

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

## 待下发 prompt 备份（Task 1 给网页版）

```
Task 1：DSA vs CSA+HCA 术语考据

已上传 2 份 PDF：
- DeepSeek_V4.pdf → DeepSeek V4 官方技术报告（58页）
- DeepSeek V4—almost on the frontier.pdf → Simon Willison 2026-04-24 评测（5页，说"CSA+HCA"的源头之一）

核对三个问题：
1. PDF 1（官方）里 "DSA (DeepSeek Sparse Attention)" 是怎么定义的？结构？
2. PDF 2（Simon）里 "CSA + HCA" 的说法从哪来——是他重命名，还是引用官方/另一篇？
3. DSA 与 CSA+HCA 是同一个东西的两套叫法，还是 DSA 包含 CSA+HCA 两个子组件？

补充线索：另一篇第三方综述 Fello AI 也用 "CSA+HCA" 术语（https://felloai.com/deepseek-v4/），
如有需要可查，但不强求——优先以两份 PDF 为准。

输出要求：
- 简短核对笔记，带 PDF 页码引用（"第 X 页，原文：...")
- 最后给一个明确结论：哪种表述是官方的、哪种是二手命名
- Markdown 格式，可直接入库为 wiki/deepseek-v4-dsa-clarification.md
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
