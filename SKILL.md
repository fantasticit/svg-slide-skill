---
name: svg-slide-skill
description: 专业幻灯片自动生成技能。当用户需要制作演示文稿/PPT/Keynote，提供主题或内容大纲时触发。通过四模块架构（slide-design 内容分析→slide-generator SVG 生成→slide-reviewer 质量审查→slide-saver 交付保存）自动生成专业幻灯片，内置自动质量审查与修复机制。
---

# Slide Generator

## 核心能力

本技能通过纯技能编排实现专业幻灯片的自动生成，包含四个核心模块：

1. **slide-design（内容分析师）** - 需求解析、内容扩写、主题定调
2. **slide-generator（SVG 架构师）** - 基于结构化数据生成 SVG 代码
3. **slide-reviewer（质量审查员）** - XML 合法性、样式规范、文字溢出、元素重叠检查
4. **slide-saver（交付专员）** - 文件保存、清单生成、预览页创建

## 触发条件

**必须同时满足以下条件：**

1. 用户明确表达制作幻灯片/PPT/演示文稿的需求
2. 用户提供了以下任一输入：
   - 主题/标题（如"2024 年 Q3 销售汇报"）
   - 内容大纲/要点列表
   - 原始文档/数据（需先解析后生成）
   - 场景描述（如"产品发布会开场 PPT"）

**不触发的情况：**

- 用户仅需修改现有 PPT 文件（使用 pptx-presentation-handler）
- 用户需要复杂的自定义动画效果
- 用户明确要求使用 PowerPoint 软件操作

## 工作流程

### Phase 0: 需求澄清

**目标：** 在动手之前把关键决策项对齐，避免结构定错后翻修。

#### 单页快速通道

**判断条件：** 用户明确说了"一页"/"单页"/"做一张"/"一页纸"等，或需求本身只适合单页（如"做一张数据看板"、"一页项目进度图"）。

**单页场景下，Phase 0 简化为：**
1. **确认是单页** — 如果用户已明确说了"一页"，直接确认，不再追问页数
2. **主题色** — 快速确认或根据场景自动选择（单页场景可以不追问，直接推荐）
3. **核心内容** — 确认这一页要展示什么信息（如果用户已经说清楚了，直接进 Phase 1）

**可以跳过的项：** 受众 & 场景（单页通常目的明确）、素材盘点（单页信息量有限）、硬约束（单页内容简单）。如果用户的输入已经足够清晰（如"帮我做一页 Q3 销售数据看板，用蓝色主题"），**可以直接跳过 Phase 0 进入 Phase 1**。

#### 多页标准流程

**⚠️ 页数确认是强制步骤，不可跳过。即使用户提供了完整大纲，也必须确认页数。禁止自行假设默认页数。**

**跳过条件：** 用户已提供完整大纲（含**明确的页数**、每页要点、受众说明）→ 直接进 Phase 1。注意：用户必须**明确说了页数**才算满足此条件，仅提供主题/标题不算。

**否则，必须向用户确认以下信息（页数为必问项）：**

1. **页数（必问！）** — 总共需要多少页？不可自行决定，必须由用户确认。如果用户不确定，可以根据场景建议（如"旅游攻略建议 8-12 页，您觉得呢？"），但最终页数必须得到用户确认。
2. **受众 & 场景** — 给谁看？什么场合？（行业内部 / 商业发布 / 学术报告）→ 决定语言风格和内容深度
3. **素材盘点** — 有没有现成的文档、数据、旧 PPT、文章链接？→ 有就基于素材提炼，没有就从零搭建
4. **主题色偏好** — 想要哪套配色？→ 见 `references/theme.md`，5 套预设可选，也可自定义
5. **硬约束** — 必须包含什么内容？不能出现什么？→ 提前规避返工

收集方式不限于逐条追问——如果用户的输入已经隐含了部分答案（比如"给投资人看的 15 分钟路演"已经回答了 2），直接提取，但**页数必须明确确认**。整合内容发给用户确认或修改（如有 request_form_input，优先使用该工具发给用户确认）。

**禁止行为：**
- ❌ 用户没说页数就自行决定生成 10 页
- ❌ 跳过 Phase 0 直接开始生成
- ❌ 仅根据"15 分钟 ≈ 10 页"的经验公式自动决定，不询问用户

### Phase 1: 内容设计（slide-design）

**输入：** 用户需求文本、提供的文档/数据

**处理步骤：**

1. **需求解析** — 识别演示场景、提取核心信息、判断情感基调
2. **内容扩写** — 将要点扩展为完整句子，确保每页信息量适中（标题 +3-5 要点），添加过渡页和总结页
3. **主题定调** — 根据场景推荐配色主题，选择字体组合，确定视觉风格
4. **⚠️ 区域填充预算（强制，不可跳过）** — 对每页的每个布局区域，检查规划的内容项数量是否达到组件最低内容量要求（详见 `references/component.md` 第十节）。具体执行：
   - 遍历每页的每个组件，查表确认数据项数量 ≥ 最低要求
   - **不达标时必须在此阶段修复**：增加数据维度、补充辅助组件、或更换组件类型
   - 禁止将"内容不足"的问题留到 Phase 2/3 再处理
   
   **示例：**
   ```
   第4页 "需求分析" — title-split B(65:35)
     左栏: bar-chart-A → 数据项 3 个 → ❌ 低于最低 5 个！
       修复: 增加 2 个产品线数据，或改为水平条形图+增加子类别拆分
     右上: pie-chart-A → 数据项 4 个 → ✅ 达标
     右下: tag-cloud → 标签 8 个 → ❌ 低于最低 12 个！
       修复: 增加相关关键词到 15 个，排 3 行×5 个
   ```

#### 单页场景的内容设计规则

**当 totalSlides = 1 时，以下规则生效：**

1. **不需要封面页、目录页、过渡页、金句页、结束页** — 单页就是内容页本身
2. **信息密度必须为 `"dense"`** — 单页需要在一页内传达完整信息，必须使用密集型布局
3. **推荐布局：** `title-body-footer`（三段式）、`dashboard`（仪表盘）、`title-split`（左右分栏）— 这三种布局最适合单页信息展示
4. **不推荐布局：** `full-page`（信息量太少）
5. **最少组件数 ≥ 5** — 单页必须有足够的视觉层次（标题 + 主组件 + 辅助组件 + 数据标注 + 装饰）
6. **标题区可以更紧凑** — 单页不需要大面积标题区，标题区高度可缩减到 H×5%（约 54px），为内容区腾出更多空间
7. **内容区扩展** — 单页的可用内容区可以从 y = H×10% 开始（而非 H×15%），增加约 54px 的可用高度

**单页 JSON 结构（简化版）：**
```json
{
  "metadata": {
    "title": "页面标题",
    "theme": "ocean-gradient",
    "totalSlides": 1,
    "aspectRatio": "16:9"
  },
  "slides": [
    {
      "layout": "dashboard",
      "variant": "A",
      "densityLevel": "dense",
      "purpose": "Q3 销售数据看板",
      "title": "Q3 销售数据总览",
      "components": ["kpi-card-A×4(顶部)", "bar-chart-A(左下60%)", "donut-chart-A(右下40%)", "sparkline×4(底部)"]
    }
  ]
}
```

**多样性规则在单页场景下不适用**（只有一页，无需考虑连续页面的布局多样性）。

#### 多页场景的内容设计规则（原有规则）

**输出：** 结构化 JSON 数据（布局 + 组件架构）

```json
{
  "metadata": {
    "title": "演示文稿标题",
    "subtitle": "副标题（可选）",
    "theme": "ocean-gradient",
    "totalSlides": 10,
    "aspectRatio": "16:9"
  },
  "slides": [
    {
      "layout": "full-page",
      "variant": "A",
      "densityLevel": "light",
      "purpose": "cover",
      "title": "封面标题",
      "components": ["art-title", "subtitle", "divider-line", "decoration", "footer"]
    },
    {
      "layout": "title-body-footer",
      "variant": "B",
      "densityLevel": "dense",
      "purpose": "业绩分析",
      "title": "Q3 业绩总览",
      "body": ["bar-chart-A(左60%)", "pie-chart-A(右40%)"],
      "footer": ["kpi-card-A×4"]
    },
    {
      "layout": "title-split",
      "variant": "B",
      "densityLevel": "standard",
      "purpose": "业务流程",
      "title": "Q4 行动计划",
      "left": ["process-flow-A(3步)"],
      "right": ["bullet-list(步骤说明)", "kpi-card-B×2"]
    }
  ]
}
```

**字段说明：**
- `layout` — 布局骨架（5 种），详见 `references/layout.md`
- `variant` — 布局变体（每种骨架有 3-6 种变体），详见 `references/layout.md`
- `densityLevel` — 视觉密度级别：`"light"` | `"standard"` | `"dense"`，决定最低组件数
- 组件名后可带变体后缀（如 `process-flow-A`、`kpi-card-B`），详见 `references/component.md`

**⚠️ 多样性规则：同一份 PPT 中，连续 3 页不得使用相同的 layout+variant 组合。**

**⚠️ 对称性规则（强制）：**
- **多列/网格布局中，各列/各行的项目数量必须相等。** 如果内容项总数无法被列数整除，必须在 Phase 1 阶段调整内容项数量（增加或减少），使其能被列数整除。
  - 示例：目录页使用左右两列 → 内容项必须为偶数（4、6、8），不允许出现左4右3
  - 示例：三栏布局 → 内容项必须为 3 的倍数
  - 示例：2×2 网格 → 必须恰好 4 项
- **如果业务上无法增减内容项**，则改用单列布局或其他不要求对称的布局变体。
- **卡片行中的卡片必须等宽等高**，水平均匀分布，间距一致。

**⚠️ 内容充实度规则（强制）：**
- **每页内容必须填满可用内容区的 80%+ 面积**（比空间利用率 70% 更严格）。
- **Phase 1 设计时，每页必须规划足够多的组件和文字内容**，确保生成后不会出现大片留白。
- **内容不足时的补充策略（按优先级）：**
  1. 增加文字描述的详细程度（每个要点从 1 行扩展到 2-3 行）
  2. 增加辅助组件（KPI 卡片、标签云、引用块、图标徽章）
  3. 增加数据可视化组件（迷你图表、进度条、趋势线）
  4. 扩大现有组件的尺寸以填满空间
- **禁止出现"标题 + 少量内容 + 大片空白"的页面。**

**布局类型：** `full-page` | `title-body` | `title-split` | `title-body-footer` | `dashboard`，每种有 3-6 种变体
→ 详见 `references/layout.md`

**可用组件：** `art-title` | `subtitle` | `bullet-list` | `paragraph` | `quote-block` | `tag-cloud` | `kpi-card` | `bar-chart` | `line-chart` | `pie-chart` | `donut-chart` | `radar-chart` | `sparkline` | `data-table` | `process-flow` | `cycle-diagram` | `hierarchy` | `tree-map` | `matrix` | `venn-diagram` | `timeline` | `comparison-table` | `vs-badge` | `person-card` | `icon-badge` | `image-placeholder` | `divider-line` | `decoration`，高频组件各有 3-6 种变体
→ 详见 `references/component.md`

### Phase 2: SVG 生成（slide-generator）

**输入：** Phase 1 输出的结构化 JSON

**处理步骤：**

1. **主题加载** — 读取主题配置（`references/theme.md`），应用全局样式变量
2. **⚠️ 坐标预计算（强制，不可跳过）** — 在写任何 SVG 代码之前，必须先用文字列出本页的坐标计算过程：
   ```
   // 示例：每页生成前必须先输出类似计算
   画布: 1920×1080
   内容区: y_start=162, y_end=961, CH=800px
   本页组件: [组件A 高度h1] + [组件B 高度h2] + [组件C 高度h3]
   组件总高度 = h1 + h2 + h3 = ???px
   自适应间距 = (800 - 组件总高度) / (N+1) = ???px
   → 组件A 起始y = 162 + 间距 = ???
   → 组件B 起始y = 组件A底边 + 间距 = ???
   → 组件C 起始y = 组件B底边 + 间距 = ???
   → 最后组件底边y = ??? (必须 > 860px 即 H×80%)
   → 上方留白 ≈ 下方留白 ✅
   ```
   **如果最后一个组件底边 y < H×80%（864px），必须增大组件高度或添加辅助组件，重新计算直到满足条件。**
3. **页面布局** — 根据 `layout` + `variant` 选择布局变体，在区域中填入组件变体，使用步骤 2 计算出的坐标（详见 `references/layout.md`）
4. **密度检查** — 根据 `densityLevel` 查视觉层次预算表，确保组件数达标（详见 `references/component.md`）
5. **SVG 代码生成** — 逐页生成 SVG，应用视觉丰富度规则，组件必须使用其必选特性（详见 `references/component.md`）

**⚠️ 环形图/饼图专项提醒：** 生成包含环形图或饼图的页面时，必须在写 SVG 代码前先用文字列出每个扇区的角度计算过程（累加法），然后逐个计算 sin/cos 坐标，最后验证相邻扇区端点一致。详见 `references/component.md` "环形图逐步计算流程" 章节。

**⚠️ 时间轴页面专项提醒（强制）：** 生成包含时间轴组件的页面时，必须按以下顺序执行：
1. 写完 `<g class="background">...</g>` 后，**立即写主轴线 `<line>`**（从 `references/component.md` 第八节复制代码模板，填入坐标）
2. 确认主轴线的 `stroke-width` ≥ 3 且有 `marker-end="url(#timeline-arrow)"`
3. 确认 `<defs>` 中包含 `#timeline-grad` 渐变和 `#timeline-arrow` 箭头标记
4. 然后才能写节点 `<circle>`、分支连接线、卡片等其他元素
**如果你发现自己在写节点或卡片但还没写主轴线，立即停下来先补上。**

**核心设计理念：** 一页幻灯片 = 页面布局骨架 + 多个组件的组合。SmartArt、图表等是"页面组件"，不是"页面模板"，不要把一个组件撑满整页。

**输出：** 每页独立的 SVG 文件

### Phase 3: 质量审查（slide-reviewer）— 必须执行！

**⚠️ 这不是可选步骤。每生成一页 SVG 后，必须立即执行审查。发现问题必须修复后再继续下一页。**

**审查大类（逐项执行，详细检查项见 `references/review-checklist.md`）：**

1. **溢出检查** — 元素不超出安全边距（x ≤ W×97%，y ≤ H×96%）
2. **重叠检查** — 文字行间距 ≥ 行高，组件间距 ≥ 20px
3. **空白检查 + 垂直居中验证** — 整页填充率 ≥ 80%，上下留白差值 < 80px，最后组件底边 > H×80%
4. **区域内部填充检查** — 每个布局区域内部填充率 ≥ 75%，图表必须搭配辅助组件
5. **对称性检查** — 多列等项、卡片等宽等高、分栏高度差 < 15%
6. **对比度检查** — 暗色主题文字亮度达标
7. **图表精度检查** — Y 轴对齐、柱高比例、环形图弧线坐标（含环形图专项 8 项验证）
8. **时间轴轴线检查** — 主轴线存在、stroke-width ≥ 3、在节点之前绘制
9. **页面风格多样性检查** — 连续 3 页不重复布局（多页 PPT 适用）
10. **箭头方向检查** — S 型流程图第二行箭头指向左

→ **完整检查清单及修复方法：** `references/review-checklist.md`

**修复流程：** 发现问题 → 定位具体元素 → 计算正确坐标 → 修改 SVG → 重新检查。**最多迭代 2 次**，仍有问题则简化布局。

### Phase 4: 交付保存（slide-saver）

**输入：** Phase 3 通过的 SVG 文件集合

#### 单页交付（totalSlides = 1）

单页场景使用轻量交付，不生成冗余文件：

1. **文件保存** — 直接保存为 `output/slide_主题描述.svg`（如 `output/slide_q3_sales_dashboard.svg`）
2. **不生成** manifest.json、preview.html、ZIP 打包

**输出：**
```
output/slide_q3_sales_dashboard.svg
```

#### 多页交付（totalSlides > 1）

**处理步骤：**

1. **文件组织** — 创建输出目录 `slides_YYYYMMDD_HHMMSS`，按页码保存 SVG，生成 manifest.json
2. **预览页生成** — 创建 HTML 预览页（缩略图网格 + 点击放大）
3. **交付准备** — 打包 ZIP，准备 deliver_artifacts 调用

**输出：**
```
output/slides_20260424_160415/
├── slide_01_cover.svg
├── slide_02_toc.svg
├── slide_03_kpi.svg
├── ...
├── manifest.json
├── preview.html
└── slides_20260424_160415.zip
```

## 使用示例

### 示例 1：单页数据看板

**用户输入：**
> 帮我做一页 Q3 销售数据看板

**技能执行：**
1. Phase 0 识别为单页场景，快速通道 → 自动推荐 Ocean Gradient 主题，直接进入 Phase 1
2. slide-design 设计为 dashboard-A 布局（KPI×4 顶部 + 柱状图左下 + 环形图右下 + 迷你趋势线底部），densityLevel = dense
3. slide-generator 坐标预计算 → 生成 1 个 SVG 文件
4. slide-reviewer 审查
5. slide-saver 轻量交付 → `output/slide_q3_sales_dashboard.svg`

### 示例 2：单页项目进度

**用户输入：**
> 做一张项目里程碑进度图，用科技紫主题

**技能执行：**
1. Phase 0 识别为单页 + 已指定主题，直接进入 Phase 1
2. slide-design 设计为 title-body-footer-A 布局（时间轴主体 + KPI×3 底部），densityLevel = dense
3. slide-generator 生成 1 个 SVG
4. slide-reviewer 审查（含时间轴轴线检查）
5. slide-saver 轻量交付 → `output/slide_project_milestones.svg`

### 示例 3：商务汇报（多页）

**用户输入：**
> 帮我做一个 2024 年 Q3 销售汇报的 PPT

**技能执行：**
1. slide-design 解析为结构化数据，推荐 Ocean Gradient 主题
2. slide-generator 生成 8 页（封面 → 目录 → 业绩总览 → 区域分析 → 行动计划 → 增长趋势 → 金句 → 结束页）
3. slide-reviewer 审查视觉丰富度 + 组件组合合理性
4. slide-saver 打包交付

### 示例 4：产品发布会（多页）

**用户输入：**
> 新产品发布会开场 PPT，产品名"智擎 AI"，科技感强一些

**技能执行：**
1. slide-design 识别为发布会场景，推荐 Tech Purple 主题
2. slide-generator 生成 10 页（封面 → 目录 → 产品理念 → 技术架构 → 能力评估 → 商业模式 → 性能指标 → 核心团队 → 金句 → 结束页）
3. slide-reviewer 确保视觉冲击力和组件搭配合理性
4. slide-saver 交付

### 示例 5：古典文学课件（多页）

**用户输入：**
> 红楼梦人物关系分析的课件 PPT，用于高中语文教学

**技能执行：**
1. slide-design 识别为教育场景，推荐 Vintage Earthy 主题
2. slide-generator 生成 8 页（封面 → 目录 → 作品概述 → 贾府家族 → 人物关系 → 故事主线 → 金句 → 结束页）
3. slide-reviewer 确保文字清晰可读 + 组件搭配合理
4. slide-saver 交付

## 与其他技能的协作

**前置技能：** pdf-parser、docx-analyzer
**后置技能：** image-generator

## 异常处理

| 场景 | 处理 |
|------|------|
| 单页场景 | Phase 0 走快速通道，Phase 1 强制 dense 密度，Phase 4 轻量交付（不生成 manifest/preview/zip） |
| 内容不足（仅一个标题） | 主动询问关键信息或基于标题智能扩写 |
| 主题冲突 | 礼貌建议更合适的主题，尊重用户最终选择 |
| 审查失败（连续 3 次） | 降级处理（简化布局），交付时说明限制 |
| 超过 20 页 | 分批生成，先交付前 10 页 |

## 参考资料

- **布局系统：** `references/layout.md` — 5 种布局骨架、坐标计算、空间利用规则
- **组件规范：** `references/component.md` — 30+ 组件的 SVG 绘制规范、视觉丰富度规则
- **主题配置：** `references/theme.md` — 5 种预制主题的配色、字体、装饰元素
- **审查清单：** `references/review-checklist.md` — Phase 3 完整质量审查清单（10 大类检查项）

## 版本历史

- **v1.0.0** (2026-04-25): 初始版本
