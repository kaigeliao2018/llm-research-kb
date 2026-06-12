---
name: 即梦 CLI（Dreamina CLI）实战研究
description: 字节跳动即梦官方 AIGC CLI 工具的装机实录、命令体系、参数矩阵、限制与坑、凯戈实战建议；2026-06-12 实测重大事实：CLI 对 standard 账号封死，需 maestro vip 才能用 generator
type: research
date: 2026-06-12
status: 装机完成 + 实测纠错 + **CLI 对 standard 账号封死**
related: claude-code-skill / dreamina-web / seedance-2.0 / jimeng-membership / maestro-vip
---

# 即梦 CLI（Dreamina CLI）实战研究

> 研究日期：2026-06-12
> 研究员：凯戈 + Claude（含 Codex 协同验证）
> 触发场景：magikid-selfmedia 装机演练；剪映 X 即梦联合会员弹窗推动调研字节 AI 工具栈
> 状态：CLI 已装机，已 login 实测，**确认 CLI generator 对 standard 账号全部封死**，必须 maestro vip 才能用

---

## ⚠️ 最重要的事实（2026-06-12 实测纠错）

**CLI 有未文档化的服务端权限门**：

```bash
$ dreamina login
已复用当前本地 OAuth 登录态。
当前登录账户信息：
user_id: 4063412744103192
vip_level: standard
total_credit: 1168
登录成功，但当前账号没有 dreamina_cli 使用权限: current account is not maestro vip
```

**关键认知**：

| 项 | 实测结论 |
|---|---|
| OAuth 登录 | ✅ 能成功（token 写入 Keychain 正常）|
| 账号信息查询 | ✅ 能读 user_id / vip_level / total_credit |
| **`user_credit` / `session` / 所有 generator** | ❌ **全部被服务端拒绝**，报 `current account is not maestro vip` |
| 限制位置 | **字节服务端**（install.sh / SKILL.md / 任何 `-h` 都查不到 maestro 字样，纯隐性门）|
| Maestro VIP 是什么 | **未确认**（候选：即梦 Pro / 剪映 X 即梦联合会员 / 字节 Maestro 平台独立会员）|

**对凯戈的实际影响**：
- 凯戈 vip_level = `standard`（即梦基础会员 = 字节体系内的 standard 等级）
- **CLI 这条路径目前对凯戈完全不可用**
- 装机演练只能走**网页版即梦**主线

**调研教训（沉淀给未来的自己）**：
- ❌ Claude 第一次查 install.sh / SKILL.md / 所有 `-h` 输出**都没有 maestro 字样**，就否定了 Codex 的判断
- ✅ Codex 的判断**对了**（即便 Codex 自己没说出依据是什么，结论是对的）
- ✅ 真相由**实测 login 返回的明文错误**揭示，**官方文档不写但服务端会告诉你**
- 教训：**未文档化的服务端限制只有实测能发现**，源码搜索是不充分的

---

## 一、项目身份

| 项 | 值 | 来源 |
|---|---|---|
| 工具名 | dreamina（CLI 可执行文件名）| 安装后 binary |
| 中文产品名 | 即梦 | 即梦 AI 网页版 |
| 官方域名 | `jimeng.jianying.com`（即梦 + 剪映）| URL 一手 |
| 安装入口 | `jimeng.jianying.com/ai-tool/install`（官网公开页）| 凯戈截图实证 |
| CDN | `lf3-static.bytednsdoc.com/obj/eden-cn/psj_hupthlyk/ljhwZthlaukjlkulzlp/dreamina_cli_beta/`（字节官方 CDN）| install.sh 源码 |
| 版本 | **v1.4.5**（release_date `2026-06-04`）| `~/.dreamina_cli/version.json` |
| Build 信息 | commit `46b5b0e-dirty`，build_time `2026-06-03T19:39:25Z` | `dreamina version` 输出 |
| 状态 | **beta**（路径含 `_beta`，commit 带 `-dirty` 表示从带未提交修改的工作目录构建）| install.sh + version 输出 |
| 母公司 | 字节跳动（剪映/即梦同源）| 公开信息 |

---

## 二、安装实录（2026-06-12 凯戈 MacBook Pro）

### 2.1 推荐安装方式（不走 `curl \| bash` 黑盒）

```bash
# 1. 下脚本到 /tmp 人肉看一遍
curl -L https://jimeng.jianying.com/cli -o /tmp/dreamina-install.sh

# 2. 跑安装
bash /tmp/dreamina-install.sh

# 3. 验证（新开终端，让 PATH 生效）
dreamina version
dreamina --help
```

### 2.2 安装副作用清单（实测）

| 路径 | 内容 |
|---|---|
| `~/.local/bin/dreamina` | 主可执行文件（Mach-O 64-bit，`darwin_amd64` Intel Mac 版）|
| `~/.dreamina_cli/dreamina/SKILL.md` | **Claude Code Skill 协议格式的 SKILL.md**（MD5 校验通过）|
| `~/.dreamina_cli/version.json` | 版本元信息（用于升级检查）|
| `~/.zshrc` | 追加 `export PATH="$HOME/.local/bin:$PATH"`（已存在则不重复）|

### 2.3 安全审计要点

| 项 | 结论 |
|---|---|
| 是否需要 sudo | ❌ 不需要，纯用户态 |
| 是否触及系统目录 | ❌ 不触及，全部在 `$HOME` 下 |
| 是否清理 macOS quarantine | ✅ 是（`xattr -d com.apple.quarantine`，标准做法）|
| MD5 校验 | ✅ SKILL.md 有硬编码 MD5 校验 |
| 多平台支持 | Darwin/Linux × amd64/arm64 + Windows amd64 |
| 隐藏注入函数 | ⚠️ install.sh 末尾有 3 个 `inject_openclaw_*` 函数，但检查 `/root/.openclaw/workspace/TOOLS.md` 存在才执行，**Mac 用户上是 noop**，不会污染系统 |

> **openclaw 注入函数的真相**：openclaw 是字节内部的 Agent 框架（猜测），install.sh 试图给它注入「Dreamina 二维码登录 + 登录完成确认」规则。在凯戈 Mac 上不存在 `/root/.openclaw/`，所以三个 inject 函数全部 silent return。

---

## 三、SKILL.md 协议确认（强信号）

`~/.dreamina_cli/dreamina/SKILL.md` 是 **100% 标准 Claude Code Skill 协议**：

```yaml
---
name: dreamina-cli
description: Use when an agent needs Dreamina（即梦） image or video generation through the dreamina CLI.
---
```

证据链：
- ✅ YAML frontmatter（`name` + `description`）
- ✅ `description` 用 "Use when..." 触发语写法
- ✅ 文件名严格大写 `SKILL.md`
- ✅ 内容结构：What this tool is for / Default workflow / Choosing the right command / Model selection rule / Async task pattern
- ✅ 强制行为规则：login 完成必须 user-visible 确认 / 不依赖 exit code 判成功 / 异步任务必须 `submit_id` + `gen_status` 双校验

### 字节做了什么（生态信号）

这意味着字节官方**第一次给 Agent 生态开了即插即用 Skill 接口**。其他 Agent 框架（Claude Code / Codex / Cline / 自建 Agent）可以直接：

```bash
ln -s ~/.dreamina_cli/dreamina ~/.claude/skills/dreamina-cli
```

即可触发 Claude Code 自动调用即梦。**这是国内 AI 创作工具首个原生 Claude Code Skill**（截至 2026-06-12 已知）。

### 凯戈当前选择（2026-06-12）

**选项 A**：**仅装 CLI，不接入 `~/.claude/skills/`**
- 理由：kaige-skills 是 git 仓库，软链进去字节升级 SKILL.md 时凯戈失去版本控制
- 未来需要 Claude Code 自动调用即梦时，再升级到选项 B（软链）或 C（复制 + 入库到 kaige-skills）

---

## 四、命令体系全景

### 4.1 内置命令（账号管理 / 会话 / 任务）

| 命令 | 用途 | 依赖 login |
|---|---|---|
| `login` | OAuth Device Flow 登录（抖音扫码）| ❌ |
| `login --headless` | 无浏览器登录（出 device_code）| ❌ |
| `logout` | 清空本地 OAuth 状态 | ❌ |
| `relogin` | 清空 + 强制重登 | ❌ |
| `user_credit` | 查当前账号积分余额 | ✅ |
| `session create/list/search/rename/delete` | 会话容器管理 | ✅ |
| `list_task` | 查历史任务 | ✅ |
| `query_result --submit_id=<id>` | 查异步任务结果 | ✅ |
| `version` | 看 build 信息 | ❌ |
| `help` | 帮助 | ❌ |

### 4.2 生成命令（Generator）

| 命令 | 输入 | 输出 | 旗舰模型 |
|---|---|---|---|
| `text2image` | prompt | image | 5.0 |
| `text2video` | prompt | video | — |
| `image2image` | image + prompt | image | — |
| `image2video` | image + prompt | video（单图驱动）| — |
| `image_upscale` | image | image（高清化）| — |
| `frames2video` | 首尾两帧 image | video | — |
| `multiframe2video` | 多 image | video（多图故事）| — |
| **`multimodal2video`** | **image + video + audio 任意混搭** | **video（全能参考）** | **Seedance 2.0** |

**multimodal2video 是核武器**：对应网页版「全能参考」功能（formerly ref2video），是 CLI 暴露的最强视频生成模式。

---

## 五、关键参数矩阵

### 5.1 `text2image` 支持组合

| 维度 | 支持值 |
|---|---|
| **model_version** | 3.0 / 3.1 / 4.0 / 4.1 / 4.5 / 4.6 / 4.7 / **5.0**（最新）|
| **ratio** | 21:9 / 16:9 / 3:2 / 4:3 / 1:1 / 3:4 / 2:3 / 9:16（8 种全覆盖）|
| **resolution_type** | 3.0/3.1 → `1k` / `2k`；4.0+ → `2k` / `4k` |
| **session** | 隔离创作历史，默认 0 |
| **poll** | 提交后阻塞等 N 秒拿结果（替代异步 `query_result`）|

**缺失**（vs Stable Diffusion / Midjourney）：
- ❌ 无 `negative_prompt`（不能反向规避）
- ❌ 无 `seed`（每次结果不同，复现性差）
- ❌ 无 `cfg_scale` / `steps`（黑盒，简化设计）

### 5.2 `multimodal2video` 支持组合

| 维度 | 支持值 |
|---|---|
| **model_version** | `seedance2.0` / `seedance2.0fast` / **`seedance2.0_vip`**（唯一支持 1080p）/ `seedance2.0fast_vip` |
| **inputs** | image≤9 + video≤3 + audio≤3（任意混搭，至少 1 image 或 1 video）|
| **audio 长度** | 2-15 秒 |
| **duration** | 4-15 秒/次 |
| **ratio** | 1:1 / 3:4 / 16:9 / 4:3 / 9:16 / 21:9 |
| **video_resolution** | seedance2.0_vip → 720p / 1080p；**其他三个只能 720p** |
| **prompt** | optional（可纯靠参考素材生成）|

**audio 输入的隐含能力**：音频驱动节奏/嘴型 → 数字人雏形。

### 5.3 `session` 子命令

```bash
dreamina session create "项目名"     # 创建（auto-naming 或自定义）
dreamina session list / ls -n 100    # 列出（默认 30，上限 100）
dreamina session search "关键词"     # 按名查 ID
dreamina session rename <id> "新名"  # 改名
dreamina session rm <id>             # 删除（历史移到 session 0 不丢）
```

- **Session 0** = 默认 session，不能改名/删
- 所有 generator 命令都接 `--session=<id>` 参数

---

## 六、限制和坑（必须盯的 5 条）

### 6.1 积分消耗未知

CLI 任何 `-h` 都**没有标出每个模型的积分消耗**。凯戈基础会员 **1080 积分/月**（下次续费 2026-07-07），**必须先做积分基线测试**：

```bash
dreamina user_credit                                                     # 起点
dreamina text2image --prompt="测试" --model_version=4.0 --resolution_type=2k --ratio=16:9 --poll=60
dreamina user_credit                                                     # 终点
# 差值 = 单张图实测积分
```

行业惯例估算（**未实测，仅参考**）：

| 配置 | 估算积分/张 |
|---|---|
| 3.0 / 1k | 1-5 |
| 4.0 / 2k | 5-15 |
| 5.0 / 4k | 20-50 |

### 6.2 ⚠️ Maestro VIP 权限门 — 实测确认整个 CLI 对 standard 账号封死

**2026-06-12 实测重大发现**：CLI 不只是 `seedance2.0_vip` 这种 VIP 模型受限，**整个 CLI generator 链路都需要 maestro vip 等级**。

```bash
$ dreamina user_credit
# 服务端返回：current account is not maestro vip
```

| 等级 | 凯戈状态 | CLI 可用性 |
|---|---|---|
| standard（即梦基础会员）| ✅ 当前 | ❌ 仅能查账号信息，所有 generator 封死 |
| maestro vip | ❌ 非此等级 | ✅ 推测能用（未实测）|

**Maestro VIP 是什么 — 候选猜测（均未确认）**：
- 即梦 Pro 会员的内部代号
- 剪映 X 即梦联合会员（2026-06-12 早上看到的 3 折弹窗）
- 字节内部 Maestro 平台的独立会员（Maestro 是字节的 AI 编排平台）

**调研 TODO**（决策时机：凯戈考虑升级会员时）：
1. 查即梦 Pro 是不是 maestro vip
2. 查剪映 X 即梦联合会员是不是 maestro vip
3. 价格 vs 单独续费的对账

### 6.3 login 是所有命令的门

`user_credit` / `session 子命令` / 所有 generator → **全部需要登录才能用**。装机演练只跑 `version` / `help` / 子命令 `-h` 完全无副作用，但只要做任何实际事情就必须登录。

OAuth Device Flow 登录路径：
- 标准：`dreamina login`（自动开浏览器扫码）
- 无头：`dreamina login --headless` + `dreamina login checklogin --device_code=<code>`
- 用**抖音 App 扫码**确认（凯戈 Coach Leo 账号）

### 6.4 -dirty 版本号警告

`46b5b0e-dirty` 后缀表示字节从**带未提交修改的工作目录**构建，不是 clean commit。说明 CLI 还在快速迭代期，版本管理偏松（属于 beta 状态）。**不适合生产关键路径**，适合实验/学习/沉淀。

### 6.5 异步任务双校验

字节官方 SKILL.md 明文要求：**不能仅靠 shell exit code 判成功**。

正确判断：
- `submit_id` 存在
- `gen_status` 是 `querying` 或 `success`
- 若 `gen_status` 是 `fail` → 必须查 `fail_reason`

异步任务流程：
```bash
# 1. 提交（拿 submit_id）
dreamina text2image --prompt="..." --poll=60     # poll 阻塞等结果
# 或不 poll，立即返回 submit_id

# 2. 查结果
dreamina query_result --submit_id=<id>

# 3. 批量回看
dreamina list_task --gen_status=success
```

---

## 七、凯戈实战建议（写给未来的自己）

### 7.1 装机演练标准操作流（SOP）

⚠️ **以下 SOP 仅适用于 maestro vip 账号**。standard 账号（凯戈当前等级）**只能走网页版**，CLI 全部 generator 被服务端拒绝。

**Maestro VIP 账号 SOP**（凯戈未来升级后启用）：

```bash
# === 一次性：装机 + 登录 + 建项目 session ===
bash /tmp/dreamina-install.sh                          # 装
dreamina version                                       # 验
dreamina login                                         # 扫码登录（Coach Leo 抖音）
dreamina user_credit                                   # 记起点积分
dreamina session create "项目名-YYYY-MM-DD"           # 项目隔离

# === 每张图卡 ===
dreamina text2image \
  --prompt="..." \
  --model_version=4.0 \
  --ratio=16:9 \
  --resolution_type=2k \
  --session=<id> \
  --poll=60

# === 项目收口 ===
dreamina list_task --session=<id>                      # 看历史
dreamina user_credit                                   # 看总消耗
```

**Standard 账号实际能做的**（凯戈当前可用）：

```bash
dreamina version                  # ✅ 看 build 信息
dreamina --help                   # ✅ 看命令体系
dreamina <subcommand> -h          # ✅ 看子命令参数
dreamina login                    # ✅ 能登录拿账号信息（但 generator 还是封死）
# 生产工作 → 走网页版 jimeng.jianying.com
```

### 7.2 不同场景的配置建议

| 场景 | 模型 | 分辨率 | 比例 |
|---|---|---|---|
| **学习图卡 / 教程素材** | 4.0 | 2k | 16:9 |
| **抖音/小红书图文发布** | 4.7 或 5.0 | 4k | 9:16 |
| **快速试错 / 积分基线** | 3.0 | 1k | 1:1 |
| **000 号视频生产**（多图叙事）| `multimodal2video` + `seedance2.0` | 720p（基础会员）| 9:16 |
| **000 号视频生产**（高质量）| `multimodal2video` + `seedance2.0_vip` | 1080p（需 Pro）| 9:16 |

### 7.3 性价比铁律

1. **教程素材不上 5.0/4k** —— 浪费积分，2k 足够课件用
2. **不上 9:16 做学习素材** —— 9:16 是发布期才用，学习阶段全用 16:9 横屏
3. **000 号选题视频生产前先升 Pro** —— 想 1080p 必须 VIP 模型
4. **每次都建独立 session** —— 项目隔离 + 积分溯源

---

## 八、未确认 / 待补 TODO

⚠️ **大多数 TODO 现在跑不了**（需要 maestro vip 等级），等凯戈未来升级会员后再启动。

| 项 | 优先级 | 何时补 | 前置条件 |
|---|---|---|---|
| **Maestro VIP 到底是什么等级**（即梦 Pro / 联合会员 / Maestro 平台）| **高** | 凯戈考虑升级会员前 | 无（可纯查证）|
| Maestro VIP 升级价格 vs 单独续费对账 | **高** | 同上 | 同上 |
| 单张图实测积分（4.0/2k）| 中 | 升级 maestro vip 后 | maestro vip |
| `seedance2.0_vip` 是否包含在 maestro vip 范围 | 中 | 同上 | maestro vip |
| 即梦网页版 vs CLI 功能差异 | 中 | 网页版主线跑完后对照 | 无 |
| CLI 生成的图/视频默认保存路径 | 中 | 升级后第一张图实测 | maestro vip |
| SKILL.md 接入 `~/.claude/skills/` 后的 Claude Code 实际触发体验 | 低 | 后期 000 号选题前 | maestro vip |
| Seedance 2.0 vs 即梦视频生成的关系（Seedance 是否就是即梦内部模型代号？）| 低 | 字节技术博客查证 | 无 |
| `text2image` default model 实测是哪一个 | 低 | 不指定 model_version 跑一次看输出 | maestro vip |

---

## 九、与其他工具的关系

### 9.1 字节 AI 工具栈定位

| 工具 | 角色 | 凯戈状态 |
|---|---|---|
| **剪映**（CapCut 国内版）| 视频剪辑端（含数字人、字幕）| Coach Leo 个人版 SVIP，到期 2026-06-21 |
| **即梦网页版** | AI 生成端（UI 优先）| 基础会员，1080 积分/月，下次续费 2026-07-07 |
| **即梦 CLI**（本研究）| AI 生成端（Agent 优先）| 已装 v1.4.5，**已登录但 standard 等级被服务端拒绝调用 generator**；需 maestro vip 才能真正使用 |
| **HeyGen** | 国际数字人工具 | 已登录（备用 / 对照学习）|
| **ElevenLabs** | 国际配音 | 备用，暂不启用 |
| **剪映 X 即梦联合会员**（3 折弹窗）| 字节官方推合并订阅 | **未决策**，等装机演练跑完再判 |

### 9.2 国内主战场链路（凯戈青少年 AI 教育 IP「K·AI」目标）

```
即梦（生成图/视频片段） → 剪映（拼接 + 数字人 + 字幕 + 音频） → 抖音 / 小红书发布
```

CLI 在此链路中的位置：**Agent 自动化生成端**。让 Claude Code / Codex 直接调用即梦生成素材，比网页版手工操作快一个数量级。

但**今天主线（装机演练）不在这条链路上跑端到端**，今天只装机 + 学习沉淀 + 留下使用范式。

---

## 十、相关引用

- 即梦 AI 官网：`https://jimeng.jianying.com/`
- CLI 安装页：`https://jimeng.jianying.com/ai-tool/install`
- 安装命令：`curl -s https://jimeng.jianying.com/cli | bash`（推荐先下载到 /tmp 看一遍）
- 本机 install.sh 副本：`/tmp/dreamina-install.sh`（396 行）
- 本机 SKILL.md：`~/.dreamina_cli/dreamina/SKILL.md`（123 行）
- 本机 binary：`~/.local/bin/dreamina`（Mach-O 64-bit darwin_amd64）

---

**研究状态**：装机 + 实测 + Maestro VIP 权限门发现完成。**CLI 对凯戈（standard 等级）实际不可用**，装机演练改走网页版即梦主线（jimeng.jianying.com）。

**下一次更新触发条件**：
- "Maestro VIP 是什么" 查证后（即梦 Pro / 联合会员 / 独立 Maestro 平台）→ 更新「六-6.2」+「八」段落
- 凯戈未来升级到 maestro vip 后实测积分消耗 → 更新「六-6.1」+「七-7.1」段落
- 即梦官方公开承认 standard 不可用 CLI 后 → 更新「⚠️ 最重要的事实」段落（标记为已文档化）

---

## 附：本次研究的元教训（沉淀给未来的自己）

**事实链**：
1. Claude 第一次查 install.sh / SKILL.md / 所有 `-h` 输出 → 无 maestro 字样 → 否定 Codex 的"maestro vip"判断
2. 凯戈实测 `dreamina login` → 服务端返回明文错误 `current account is not maestro vip` → Codex 判断成立
3. Claude 立刻承认错误，纠正所有此前结论

**违反的铁律**：
- 触发了 [[feedback_research_rigor]] 反面：源码搜索"找不到"≠"不存在"，**未文档化的服务端限制只有实测能发现**
- 触发了 [[feedback_truth_first]] 正面：发现错误立刻摆正，不为面子拖延

**学到的新规则**（建议沉淀为新 memory）：
- **"源码不出现 X 字样" 不能否定 "服务端有 X 限制"**
- 反之亦然：服务端的隐性限制（权限门、配额门、地区门）通常只在 API 返回的 error message 里暴露
- 调研服务端限制的唯一可靠路径 = 实测 + 读 error log，**不是** grep 源码
