# LLM Research KB — 研究日志

## 2026-04-21

- 项目初始化
- 起因：调研智谱清言 GLM Coding Plan，可将 Claude Code 接入 GLM 模型
- 参考文档：
  - GLM-5.1 接入指南：https://docs.bigmodel.cn/cn/coding-plan/using5-1
  - Claude Code 接入方案：https://docs.bigmodel.cn/cn/coding-plan/tool/claude
- 后续：整理 GLM Coding Plan 可行性分析 → wiki/zhipu-glm-coding-plan.md

## 2026-04-24

- DeepSeek V4 Preview 发布当日研究，产出 `wiki/deepseek-v4.md`
- 信息源（5 个）交叉核对：官方公告 + HF 模型卡（Pro/Flash）+ Simon Willison 评测 + Fello AI 综述
- 核心结论：
  - V4-Pro（1.6T/49B）与 V4-Flash（284B/13B）双变体，MIT license
  - 1M context 原生；效率相比 V3.2 大幅改善（Pro: 27% FLOPs, 10% KV cache）
  - 编程最强（Codeforces 3206，LiveCodeBench 93.5%），知识/通识略逊 Gemini 3.1 Pro
  - 价格极低：Flash $0.14/$0.28，Pro $1.74/$3.48
  - 兼容 OpenAI + Anthropic API 协议；已集成 Claude Code / OpenClaw / OpenCode
  - 老模型 deepseek-chat / deepseek-reasoner 将于 2026-07-24 下线
- 待核实：
  - DSA 与 CSA+HCA 的术语对应关系（需读 tech report PDF）
  - 官方 pricing 页面与 Simon 数字的一致性

### 2026-04-24 下午 · 网页版外勤启动

- 开通网页端 Claude Projects "llm-research-kb"，Mac 本地 ↔ 网页版双端分工确立
  - Mac（Claude Code）：写文件、git、接入实测
  - 网页版：读 PDF、爬中文评测、搜集多源信息
- Mac → 网页版已交付：Project 描述、Custom Instructions、第一轮同步信息
- 网页版首轮回执确认，要求补素材后开工

### 素材准备状态
- ✅ 凯戈手工下载 2 份 PDF 至桌面：
  - `~/Desktop/DeepSeek_V4.pdf`（58 页，4.5MB，官方技术报告）
  - `~/Desktop/DeepSeek+V4—almost+on+the+frontier,+a+fraction+of+the+price.pdf`（5 页，Simon Willison 博客存档）
- ✅ Mac 已写好 Task 1 给网页版的 prompt（本对话内，待凯戈出门归来复制）
- ⏸ Task 1 尚未下发给网页版（凯戈需手工拖 PDF + 粘贴 prompt）
- ⏸ Task 2（pricing 核对）、Task 3（Claude Code × DeepSeek V4 接入）待 Task 1 收工后并行

### 收工状态（凯戈出门前）
- 本地无未 commit 变更？待确认（本轮未 git push）
- 30min push 提醒：本轮未启用（开场流程被打断）
