# pdf Skill vs fitz 实测对比报告

> 实测日期：2026-05-05
> 测试者：凯戈 + Claude Code（CLI）
> 输入：`VIQRC-level-up-0.1-manual.pdf`（93 页 / 9.33 MB）+ 中文版（73 页 / 3.42 MB）对照
> 目的：评估 Anthropic 官方 `pdf` skill vs fitz（PyMuPDF）基线，确定 vex-iq-kb Game Manual 入库主力工具
> 相关：[[skills]] §10.1（评分摘要）

---

## 一、测试栈

| 路径 | 工具组合 | 来源 |
|------|----------|------|
| **pdf skill** | pypdf 6.9.2 / pdfplumber 0.11.9 / pdfimages（poppler）/ pdftotext / pytesseract（未启用） | `document-skills:pdf`（Anthropic 官方 marketplace） |
| **fitz 基线** | PyMuPDF 1.27.2.2 单库 | karpathy-kb-research 既有管线 |

---

## 二、四维结果汇总

| 维度 | pdf skill | fitz | 谁赢 |
|------|-----------|------|------|
| **元数据 / 页数** | pypdf：即时 | fitz：即时 | 平 |
| **文本抽取** | pdfplumber：31s · 166k 字符 / pdftotext-layout：191k 字符 | 1.1s · 168k 字符 | **fitz 速度赢 28×**，质量近似 |
| **表格抽取** | pdfplumber：38 个 / 31 页 | find_tables：13 个 / 8 页 | **fitz 完胜（精度 100% vs ~34%）** |
| **图像抽取** | pdfimages：233 张（48 唯一） | get_images：124 张（33 唯一） | 召回平手，fitz 易用性赢 |
| **总耗时** | ≈ 75s | ≈ 16s | **fitz 4.7×** |

---

## 三、深挖结论

### 3.1 表格：pdfplumber 多出来的全是噪声

- fitz 13 个表 ⊂ pdfplumber 38 个表（**fitz 是真子集，无遗漏**）
- pdfplumber 多出 25 个全是误判：
  - **版权框**（11 个）：每页底部 `Copyright 2026, VEX Robotics Inc.` 的 3×3 装饰框
  - **Violation notes 红框**（14 个）：「This rule has additional Violation notes. See Appendix D.」单句说明

**修正后真表数**：fitz 13 = pdfplumber 真表 13（fitz 精度 100%，pdfplumber 精度 ~34%）

### 3.2 图像：双方重复都严重

| | 文件数 | 唯一图（phash 去重）| 重复倍数 |
|--|--------|---------------------|---------|
| pdfimages | 233 | 48 | 4.85× |
| fitz | 124 | 33 | 3.76× |

最重复的 phash `852fc746`：pdfimages 出现 164 次、fitz 82 次 —— 推测是 VEX logo / 页眉装饰带，每页一份。

### 3.3 金标准验证：figure-R3-1 / figure-R4-1

| 工具 | R3-1 命中 | R4-1 命中 | 自带页码 | 格式 |
|------|----------|----------|---------|------|
| pdfimages | `img-112.jpg`（d=0）| `img-116.jpg`（d=0）| ❌ | .ppm（不直用）|
| fitz | `page048_img00.png`（d=0）| `page049_img00.png`（d=0）| ✅ | PNG（直用）|

凯戈手动翻 PDF 验证：物理页 48 = R3-1 size limit，物理页 49 = R4-1 license plate ✅

---

## 四、中文版对照（反证噪声根因）

中文版 PDF（73 页）跑 pdfplumber：

| 维度 | 中文版 | 英文版 |
|------|--------|--------|
| 表格总数 | **14（全是真表）** | 38（含 25 误判）|
| Violation notes 误判 | **0** | 14 |
| 版权框误判 | **0** | 11 |
| 文本质量 | 0 乱码 | 0 乱码 |
| 速度 | 19s | 31s（页数多）|

**结论**：英文版 25 个误判的根因是**英文版 PDF 排版工艺**（红框 + 装饰底框），不是 pdfplumber 算法缺陷。中文版排版没有这些装饰元素，误判自动归零。

---

## 五、综合评分

| 子项 | pdf skill | fitz | 备注 |
|------|-----------|------|------|
| 文本质量 | ★★★★ | ★★★★ | 平 |
| 文本速度 | ★★ | ★★★★★ | fitz 28× |
| 表格精度 | ★★ | ★★★★★ | fitz 完胜 |
| 表格速度 | ★★★ | ★★★★ | fitz 3× |
| 图像召回 | ★★★★ | ★★★★ | 平 |
| 图像易用性 | ★★ | ★★★★ | fitz 自带页码 + PNG |
| 中文支持 | ★★★★★ | ★★★★★ | 平 |
| OCR 能力 | ★★★★（pytesseract，仅扫描件场景）| ★（无原生）| **2026-05-07 补测**：born-digital PDF 跑 OCR 反而劣化（见下文 §六.5）|
| 表单处理 | ★★★★★（forms.md）| ★★ | skill 加分项（本次未实测）|
| **综合** | **3.4 ★** | **4.0 ★** | **当前 VEX 任务 fitz 主选** |

---

## 六、决策矩阵

| 场景 | 选谁 | 理由 |
|------|------|------|
| 积分表 + 图示密集的 born-digital PDF | **fitz** | 精度高、自带页码、单库部署；**不要切 OCR**（§六.5）|
| 扫描 PDF / 无文本层 PDF | **pdf skill** | fitz `get_text()` 返回空时唯一选择 |
| 图内嵌字（Figure 标签 / 截图旁注） | **pdf skill OCR 局部** | fitz 不能读栅格图内文字，整页 OCR 反劣化 |
| 表单填写 | **pdf skill** | forms.md + 8 个填表脚本 |
| 中文 PDF | 任选 | 两者中文支持均 ★★★★★ |

### 六.5 OCR 维度补测（2026-05-07）

跟进表中 OCR 行"本次未实测"遗留项。物理页 21（Scoring 章节，文字 + 计分表 + Figure SC3-1）单页对比：

| 维度 | fitz | pytesseract OCR (300 dpi) |
|---|---|---|
| 字符数 | 2159 | 2045 |
| 计分表 5 行 + 5 个分数 | ✅ 全到位 | ❌ 只读 3 行，**5 个分数全丢** |
| 装饰底纹 | 无干扰 | 误读为 `e e e e e` / `YUL ALLL LLL...` |
| 速度 | ~50 ms/页 | ~5 s/页（100×慢）|
| 平均置信度 | N/A | 92.6% |

**反直觉结论**：born-digital PDF 跑 OCR 反而劣化。装饰元素干扰像素级算法（OCR / pdfplumber），但不干扰原生文本流（fitz）—— 与 §四 表格精度一节"pdfplumber 25 个表误判"同根因。

**OCR 真正用武之地**：① 扫描件 / 文本层缺失；② 图内嵌字（Figure 标签）局部 OCR；③ 截图入库（学生上传赛规截图）。

完整数据：`~/vex-iq-kb/raw/game-manual/skill-test/ocr-test/REPORT.md`（本地，gitignore）

---

## 七、意外发现：marketplace 安装是 all-or-nothing

装一个 `document-skills` plugin 实际把仓库 `skills/` 目录下的 **17 个 skill 全部注入会话**。`marketplace.json` 的 `skills` 字段不约束加载范围，整个仓库扫描。

影响：`skills.md §7`「不装清单」在 Anthropic 官方 marketplace 路径上**机制不可执行**。

当前缓解策略（方案 C）：接受全装 + 用法约束（skill 仅 metadata 注入，不主动执行，行为污染可控）。

---

## 八、本次产物清单（本地，gitignore）

```
~/vex-iq-kb/raw/game-manual/skill-test/
├── run_compare.py              # 4 维对比测试脚本
├── compare_report.json         # 原始数据
├── REPORT.md                   # 原始完整报告
├── skill-out/
│   ├── text_pdfplumber.txt
│   ├── text_pdftotext_layout.txt
│   ├── tables_pdfplumber.json  # 38 表（13 真 + 25 噪声）
│   └── images_pdfimages/       # 233 张 .ppm
├── fitz-out/
│   ├── text_fitz.txt
│   ├── tables_fitz.json        # 13 表（全真）
│   └── images_fitz/            # 124 张 .png
└── ocr-test/                   # 2026-05-07 OCR 补测
    ├── run_fitz.py / run_ocr.py
    ├── fitz_text.txt / ocr_text.txt
    ├── fitz_tables.json / ocr_data.json
    ├── page21_render.png / page21_300dpi.png
    └── REPORT.md               # OCR 补测报告
```

---

## 关联词条

- [[skills]] §10.1 — 评分摘要（本页详细版）
- [[skills-howto]] — Skill 安装操作手册
