# Codex 官方更新详细报告（2026-05-14 → 2026-06-12）

> **信息截止**：2026-06-11 19:12 UTC（北京时间 6-12 03:12）
> **最新 CLI release**：`rust-v0.140.0-alpha.11`（2026-06-11）
> **最新 stable**：`rust-v0.139.0`（2026-06-09）
> **最新 App**：Codex app 26.609（2026-06-11）
> **最新 iOS**：ChatGPT for iOS 1.2026.153（2026-06-09）

## 数据源对账

| 来源 | 状态 | 用处 |
|---|---|---|
| `gh api repos/openai/codex/releases` | ✅ 拿到最近 30 个 release | CLI 时间线 |
| OpenAI 官方 changelog (developers.openai.com/codex/changelog) | ✅ 拿到 5/14 - 6/11 完整 | App / Mobile / 大新闻 |
| WebSearch 多源交叉 | ✅ | 交叉验证 |
| OpenAI 6 月升级 blog (`introducing-upgrades-to-codex`) | ❌ 403 拿不到 | 已剔除 |

---

## TL;DR

这 4 周官方没出新模型（最近一次是 4-23 的 GPT-5.5），全部精力砸在**产品化和生态铺设**上：

1. **三端同步密集迭代**：CLI 几乎日更（alpha 流水线）+ 周更稳定版；Desktop App 每周 1-2 版；ChatGPT iOS 每周 1 版
2. **重磅新产品 Sites**（6-2 预览版）——OpenAI 自己挂网站/dashboard/小游戏的托管平台
3. **Goal mode 转 GA**（5-21）——Codex 可"驱动数小时到数天的目标"
4. **Computer Use 攻 Windows**（5-29）——可操控 Windows 桌面应用
5. **拉新策略明牌**：6-9 上线 "**Migrate to Codex from Claude Code / Claude Cowork**"——直接挖 Claude 用户

---

## 一、CLI 发版节奏（每天用的那个）

| 版本 | 日期 | 类型 | 关键变化 |
|---|---|---|---|
| 0.140.0-alpha.7-11 | 6-10 / 6-11 | alpha | 4 个 alpha 一天连发，正在打磨 0.140 |
| **0.139.0** | **6-9** | **stable** | Code mode 直调 web search；MCP `oneOf/allOf` 保留；`codex doctor` 升级；plugin marketplace JSON |
| **0.138.0** | **6-8** | **stable** | `/app` 命令切 Codex Desktop（macOS / Win）；图片附件路径暴露；reasoning effort 灵活；v2 PAT |
| **0.137.0** | **6-4** | **stable** | F13-F24 键位；企业月度信用额度；远控配对 v2；Multi-Agent v2 持续优化；标准 web/image 工具支持并行 |
| **0.136.0** | **6-1** | **stable** | **会话归档**（`/archive`、`codex archive`）；`app-server --stdio`；`CODEX_API_KEY` 远程注册；Windows sandbox 提权 alpha；TUI OSC 8 可点链接 |
| **0.135.0** | **5-28** | **stable** | `codex doctor` 诊断详尽化；`/status` 显远程信息；Vim text-object 编辑；`/permissions` 配置 profile；Python SDK Sandbox preset；`CODEX_NON_INTERACTIVE=1` 静默装机 |

**节奏判断**：稳定版周更（5-28→6-1→6-4→6-8→6-9，5 天 5 个 stable），alpha 在 stable 之间几乎日更。属于"主动管理风险"的发版策略——稳定版给企业，alpha 给极客滚动尝鲜。

---

## 二、Desktop App 主线大事

### 🚀 5-21（Codex app 26.519）—— 季度级别发布
- **Appshots**（macOS 独占）：双击 ⌘ 把前台窗口截图 + 文字直接打给 Codex，免去复制粘贴
- **Goal mode GA**：跨 App / IDE / CLI 全线提供，长周期目标自动驱动数小时-数天
- **Remote Computer Use**：Mac 锁屏后还能远程通过 Codex Mobile 让它操控桌面应用
- **Plugin marketplace 共享**：ChatGPT Business 可发布插件供工作区内部安装
- **In-app browser 高级标注**：可标 font size、颜色、间距给视觉反馈

### 5-29（26.527）—— Windows 攻坚
- Computer Use **正式登陆 Windows**
- 远控也支持 Windows 设备
- Chrome 抓 Docs/Sheets/Slides 上下文增强

### 6-1（26.601）+ 6-2 Sites + 6-1 Bedrock
- **Sites 预览**：OpenAI 自己挂网站/dashboard/内部工具/小游戏
- **Amazon Bedrock 模型源**：可用 AWS 账号跑 Codex
- Terminal 默认位置设置

### 6-4（26.602）
- Activity insights + Profile 分享卡

### 6-9（26.608）—— 拉 Claude 用户的动作
- **Migrate to Codex from Claude Code / Claude Cowork**：onboarding 时可直接迁移配置
- 插件界面大改版（tab、市场分类、键盘导航）

### 6-11（26.609）—— 浏览器+生产力
- Browser **Developer mode**：可控的 Chrome DevTools Protocol 访问
- Browser use **2 倍速**（CDP + DOM snapshot 优化）
- App 内 `/init` 命令对齐 CLI 的项目初始化
- 企业 Computer Use 扩区（EEA/UK/CH 之外）+ Windows 每应用访问控制
- Plus/Pro **rate-limit 重置银行**：launch 时 1 次免费重置 + 推荐码可挣更多

---

## 三、ChatGPT iOS（Codex Mobile）

| 日期 | 版本 | 关键 |
|---|---|---|
| 5-14 | — | **远程连接**：手机连 Mac 上的 Codex App，项目/凭据/插件全继承；Hooks GA；Codex access token；企业管理员配置指引 |
| 5-18 | 1.2026.132 | 完成通知开任务；改文件直接看 |
| 5-25 | 1.2026.139 | Spotlight / Shortcuts 开 Codex Mobile；`/side`；图保存 |
| 6-2 | 1.2026.146 | Face ID 锁；`/side <prompt>`；SSH 进 Windows 主机 |
| 6-9 | 1.2026.153 | 分支选择 + worktree + env 脚本；Codex profile 屏；`/goal` 移动管理；编辑最近发出的 prompt |

---

## 四、按主题分类（决策视角）

### A. Multi-Agent v2 持续打磨
- 0.137（6-4）：每线程保留 runtime 选择，spawned agent 的 follow-up/metadata 默认值更干净
- 0.139（6-9）：MCP 启动告警留在所属子线程不再串扰；并发按"活跃执行"计数；`close_agent` 重命名为 `interrupt_agent`；resume 不再误开 v2 后代线程

### B. Web / Browser 工具链
- 0.139（6-9）：**Code mode 可直调 standalone web search**（含嵌套 JS 工具调用），返回纯文本
- 0.137（6-4）：标准 web / image 工具能并行
- 6-11 App：Browser Developer mode + 2x 速度

### C. 桌面 ↔ 移动 ↔ CLI 联动
- 0.138（6-8）：CLI `/app` 命令切 Desktop（macOS / Win）
- iOS 5-14 起：手机能远程操控 Mac 上的 Codex
- 5-21 Appshots：截图直接喂 Codex
- 5-29 / 6-11：Windows Computer Use 全线打通

### D. Plugin Marketplace（生态战）
- 5-21：marketplace 共享给 ChatGPT Business
- 6-9：插件界面大改版
- 6-11：本地插件可分享给整个 workspace；管理员可通过 MDM/云配置禁用

### E. Sites（新产品线）
- 6-2 预览：托管 web app / 仪表盘 / 工具 / 小游戏；ChatGPT Business 默认包含；Enterprise RBAC 可控

### F. 云供应商扩张
- 6-1：Amazon Bedrock 成为可选模型源

### G. 拉 Claude 用户
- 6-9：Migrate to Codex from **Claude Code / Claude Cowork**——onboarding 直接迁移配置

---

## 五、凯戈视角 · 该看哪几样（CTO 决策点）

按"团队 CTO + Codex 派单"的工作流，下面这 5 件值得评估：

1. **CLI 0.138 `/app` 切 Desktop**——如果装了 Desktop App，可以在 CLI 跑一半把任务推给桌面端继续。匹配"Mac 主力 + 课件视觉交 Codex"的场景

2. **CLI 0.139 Code mode 直调 web search**——Codex 自己能查官方资料，可能减少"先喂 PDF / 先帖链接"的次数。但要观察是否会和"研究铁律：先看官方原文"打架（默认 web search 不一定权威）

3. **Goal mode GA**（5-21）——长周期目标。"赛季机型扫描 2 周节奏"完美场景，但风险是 Goal mode 会自己走偏。先在低风险任务（如 KB 入库）上小流量验证

4. **Multi-Agent v2 持续优化**——正是"Claude+V4+Codex+Gemini 派单矩阵"的对照组。Codex 自己也在搞团队矩阵，等 v2 稳定后值得对比

5. **Sites 预览**（6-2）——课件托管可选项。现在挂 Cloudflare Pages / magikidlab.com，Sites 是 OpenAI 自己的托管。但是否能自定义域名 / 是否有审计 / 是否能脱离 ChatGPT 直发学生，待查

**不建议立即跟**：
- Migrate from Claude Code（6-9）——反向（Claude CTO + Codex 团员），不适用
- Bedrock（6-1）——纯企业云账户场景
- Computer Use Windows（5-29）——不在 Windows 主战

---

## 六、本机版本核对

待办：`codex --version` 看本机版本，对照 0.139.0 / 0.140.0 决定升级时机。

---

## 来源链接

- [openai/codex GitHub Releases](https://github.com/openai/codex/releases)
- [Codex Changelog – OpenAI Developers (官方)](https://developers.openai.com/codex/changelog)
- [Codex Updates by OpenAI - Releasebot (社区聚合)](https://releasebot.io/updates/openai/codex)
- [Codex Changelog — releases.sh (社区聚合)](https://releases.sh/openai/openai-codex-changelog)
