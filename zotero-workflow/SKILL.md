---
name: zotero-workflow
description: "Zotero → Obsidian 论文笔记自动化管道。3 种模式：(1) single — 对单篇论文精读并写入 Obsidian；(2) batch — 批量处理 Zotero 分类下所有未处理论文，含断点续传；(3) query — 语义搜索/检索 Zotero 库。依赖 zotero-mcp (54yyyu/zotero-mcp) 提供 Zotero 数据接口；依赖 Obsidian MCP 写入笔记。"
metadata:
  version: "1.0.0"
  last_updated: "2026-06-15"
  status: active
  task_type: pipeline
  mcp_dependencies:
    - zotero-mcp (54yyyu/zotero-mcp) — Zotero 数据获取与搜索
    - obsidian-skills obsidian — Obsidian 文件写入
---

# Zotero Workflow — Zotero → Obsidian 论文笔记管道

合并自原 `zotero-data-fetcher` + `zotero-analytical-writer` + `zotero-collection-manager`，以 zotero-mcp 替代 Python 脚本依赖。

## 路径配置

以下路径取自现有 Obsidian vault 结构（如 `zotero-note` skill 所定义）。使用前请确认环境变量 `ZOTERO_SUITE_VAULT_ROOT` 已设置，或按实际路径替换。

| 变量 | 相对路径 | 说明 |
|------|----------|------|
| VAULT_ROOT | `<ZOTERO_SUITE_VAULT_ROOT>` 或用户指定 | Obsidian vault 根目录 |
| NOTES_ROOT | `<VAULT_ROOT>/03-科研笔记/文献笔记` | 笔记输出目录 |
| INDEX_ROOT | `<VAULT_ROOT>/03-科研笔记` | Dataview 索引页所在目录 |
| TEMPLATE_PATH | `<VAULT_ROOT>/08-Assets/Templates/论文精读模板.md` | 精读模板位置（项目中已复制一份在 `new/templates/`） |

若 `ZOTERO_SUITE_VAULT_ROOT` 未设置，在操作开始时向用户确认 vault 路径。

## 依赖

### 必需
- **Zotero 桌面端** 须正在运行，且启用 Local API（Edit → Preferences → Advanced → Allow other applications on this computer to communicate with Zotero）
- **zotero-mcp** — 提供 Zotero 数据接口（session 中通过 `zotero_*` 工具访问）
- **Obsidian MCP** 或 Write 工具 — 用于向 Obsidian vault 写入笔记文件

### 可选
- **formula_ocr MCP** — 如需从截图提取 LaTeX 公式（非本 skill 内置）

---

## 操作模式

| 模式 | 触发方式 | 适用场景 |
|------|---------|---------|
| `single` | "精读这篇论文" + 提供 Zotero Item Key 或 DOI | 单篇论文深度阅读并写入笔记 |
| `batch` | "批量处理 [分类名]" | 处理 Zotero 某分类下所有未完成论文 |
| `query` | "在 Zotero 中搜索 [关键词]" | 检索、语义搜索、查看库状态 |

---

## 模式 1: single — 单篇论文精读

### 流程

```
Step 1: 获取数据 (通过 zotero-mcp)
Step 2: 分析语料并生成笔记
Step 3: 写入 Obsidian + 刷新索引
```

### Step 1: 获取数据

调用 zotero-mcp 工具获取论文完整信息：

1. **定位条目**：通过 `zotero_search_items`（关键词/标题）或 `zotero_get_collection_items`（已知分类+序号）获取 Item Key
2. **获取元数据**：`zotero_get_item_metadata`（格式选 `markdown`），提取 title / author / year / DOI / abstract / tags
3. **获取原文内容**：
   - `zotero_get_item_fulltext` — 获取全文缓存
   - `zotero_get_annotations` — 获取高亮和批注（最高优先级）
   - `zotero_get_notes` — 获取用户笔记
4. **构建 Raw_Data_Buffer**：整合上述内容为原始语料

> ⚠️ **数据提取约束**：保持论文原始语言（通常为英文），**严禁在此步骤进行翻译、总结或模板套用**。

### Step 2: 生成笔记

按以下步骤生成笔记正文。

#### 2a. Frontmatter 属性提取

必须经过深度思考与提炼，**严禁直接大段复制摘要**。每个字段限制在一句话（10-30 字）以内：

| 字段 | 定义 | 错误示范 | 正确示范 |
|------|------|---------|---------|
| **`theme`** | 一句话概括核心研究问题 | 复制整段摘要开头 | 探讨城市创新空间生态位适宜性的评价指标与空间格局 |
| **`study_area`** | 严格依据标题和摘要提取真实地名/样本范围 | 北京、上海等 | 江苏省南京市 |
| **`data_source`** | 仅提取数据提供方、数据库名称或时间跨度 | 作者单位+邮编+摘要... | 南京市统计局数据及相关地理空间矢量数据，年份为 2020 年 |
| **`methodology`** | 必须具体到模型名称或分析工具，严禁与 theme 重复 | 基于生态位视角探讨... | 构建 3 个维度的评价指标体系，结合 GIS 空间分析方法 |
| **`core_variable`** | 具体的自变量、因变量或评价维度，严禁堆砌 Keywords | 创新经济地理、创新生态系统 | 资源生态位、环境生态位、技术生态位适宜度 |
| **`key_finding`** | 最精炼地总结最重要的结论，去掉"结果表明"等废话 | — | — |
| **`relevance`** | 说明该文的具体闪光点 | 可用于补充文献脉络 | 提供了南京的对比基准 / 提供了生态位测度指标 |

**强制校验机制**：写入前核对 `data_source` 是否混入"摘要："、"作者："等无效字符；核对 `study_area` 是否与标题地名冲突。如发现，必须重写该字段。

#### 2b. 模板套用

模板路径：`<TEMPLATE_PATH>`（即 `<VAULT_ROOT>/08-Assets/Templates/论文精读模板.md`）。项目中 `new/templates/论文精读模板.md` 是同一模板的副本。

##### 数学公式处理

1. **无效公式判定**：若提取到的公式仅为孤立求和号（`\sum`）、极短残片或无意义字母堆叠 → **强制判定为无效乱码**，禁止强行套用 `$$...$$`
2. **连带删除机制**：公式被判定无效或无公式时，**必须彻底删除该公式对应的"公式拆解""符号代表什么"等下级区块**
3. **如有 OCR 能力**：若用户提供 `image_path` 且有 `formula_ocr` MCP 可用，优先调用
4. **占位底线**：遇乱码且无截图时，仅输出 `$$\text{【公式复杂/乱码，如需精准提取请截图并调用 formula_ocr】}$$`

##### 结论结构

灵活调整条目数，采用成对结构（主要发现 + 原文引用）。引用附带 `zotero://open-pdf/library/items/<pdfKey>?page=<页码>`。

#### 2c. 正文内容纯净度控制

**学术垃圾黑名单**：绝对禁止将以下内容写入笔记的分析字段：作者姓名、工作单位（如"XX大学XX学院"）、通讯地址、邮编、邮箱、基金项目编号、期刊投稿须知或排版规范。

**提炼而非凑数**：从原文"数据与方法"章节提取真正的研究步骤和数据集名称。若缓存文本不含具体数据来源，如实写"缓存文本未包含具体数据来源，需查阅完整正文"，绝不能拿作者简介和基金号凑数。

### Step 3: 写入与终检

1. **路径指定**：写入 `<NOTES_ROOT>/<分类名称>/<论文标题>.md`。分类名称从 Zotero Collections 字段或用户指定获取。
2. **后置校验**：
   - [ ] Frontmatter 必填概括字段是否为全中文？
   - [ ] 是否有未闭合的 `$` 符号？
   - [ ] 是否包含有效跳转的 `zotero://` 链接？
3. 校验通过 → 执行写入
4. **索引页刷新**（新增论文时强制执行）：刷新 `<INDEX_ROOT>` 下 4 个 Dataview 索引页：
   1. `文献索引.md`
   2. `研究主题索引.md`
   3. `研究方法索引.md`
   4. `字段补全检查.md`

---

## 模式 2: batch — 批量处理 Zotero 分类

### 流程

```
Step 1: 初始化 — 定位分类 + 读取断点日志
Step 2: 获取分类条目 + 计算待处理队列
Step 3: 循环调度 — 对每篇未处理论文执行 single 模式 Step 1-3
Step 4: 输出执行报告
```

### Step 1: 初始化

1. 用户指定 Zotero 分类名称
2. 调用 `zotero_get_collections` 确认分类是否存在
3. 设定目标路径：`<NOTES_ROOT>/<分类名称>/`
4. 读取断点日志：在目标路径下查找 `_ProcessLog_进度记录.md`
   - 若存在，解析已标记为 `✅ 成功` 或 `⚠️ 跳过` 的论文 Item Key
   - 若不存在，创建该文件

### Step 2: 计算待处理队列

1. 调用 `zotero_get_collection_items` 获取该分类下所有条目清单
2. 将总清单与进度日志比对，剔除已完成条目
3. 待处理队列为空时提示用户

### Step 3: 循环调度引擎

对每篇论文**严格串行**执行，每处理完一篇**立即更新日志**：

1. 获取数据：`zotero_get_item_metadata` → `zotero_get_annotations` → `zotero_get_item_fulltext`
   - 异常捕获：抓取失败（无附件、无缓存）→ 日志记录 `❌ 失败（原因）`，跳过
2. 生成笔记：执行 single 模式 Step 2
3. 写入 Obsidian：执行 single 模式 Step 3
4. 实时打卡：在 `_ProcessLog_进度记录.md` 追加：
   ```
   - [x] 2026-06-15 14:00 | ✅ 成功 | ABCD123 | 论文标题
   ```
5. 刷新 4 个 Dataview 索引页（新增文件时强制执行）

### Step 4: 执行报告

全部循环结束后输出：
- 本轮总计发现未处理文献 [X] 篇
- ✅ 新增成功：[数量] 篇
- ❌ 新增失败：[数量] 篇
- 进度已实时同步至日志文件，下次执行将自动跳过已成功条目

---

## 模式 3: query — 检索与搜索

使用 zotero-mcp 的搜索工具快速定位文献或获取概览：

| 你想做什么 | 使用工具 |
|-----------|---------|
| 按关键词搜索 | `zotero_search_items` |
| 复杂条件搜索（作者+年份+标签） | `zotero_advanced_search` |
| 获取某分类下所有论文 | `zotero_get_collection_items` |
| 语义搜索（AI 相似度） | `zotero_semantic_search` |
| 查看某论文元数据 | `zotero_get_item_metadata` |
| 查看某论文批注 | `zotero_get_annotations` |
| 查看最近添加 | `zotero_get_recent` |
| 查找重复条目 | `zotero_find_duplicates` |
| 通过 DOI/URL 添加论文 | `zotero_add_item_by_doi` / `zotero_add_item_by_url` |
| 查看数据库状态 | `zotero_get_search_database_status` |

---

## 与原 3 个 Skill 的主要变更

| 变更项 | 原实现 | 合并后 |
|--------|-------|--------|
| **数据提取** | 依赖 `prefs.js` + `extract_item_json.py` 等脚本 | 通过 zotero-mcp MCP 工具直接获取 |
| **硬编码路径** | `D:\ResearchVault\`（Windows 绝对路径） | 改为 `<Obsidian Vault>` 占位，由用户配置 |
| **技能拆分** | 3 个独立 SKILL.md，需手动串接 | 1 个 SKILL.md + 3 种模式 |
| **batch 日志** | 文件系统读写 + 自定义格式 | 保留断点续传日志格式，写入逻辑不变 |
| **全文缓存** | 直接读取 `.zotero-ft-cache` | 使用 `zotero_get_item_fulltext` |
| **批注提取** | 解析 Zotero SQLite | 使用 `zotero_get_annotations` |
| **公式 OCR** | 硬性要求 `formula_ocr` MCP | 降级为可选（检测到 `formula_ocr` 时优先使用） |

---

## 与 academic-paper 的关系

本 skill 生成的 Obsidian 论文笔记可作为 `academic-paper` 管道的文献输入：
- 笔记中的 `theme`/`methodology`/`core_variable`/`key_finding` 可被 `literature_strategist_agent` 用于文献综述阶段
- 笔记文件的 Tags 和 Dataview 索引支持跨论文的主题检索
