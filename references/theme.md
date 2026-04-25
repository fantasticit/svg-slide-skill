# 主题系统（Theme System）

## 5 种预制主题

| 主题 | ID | 主色 | 适用场景 |
|------|-----|------|----------|
| 海洋渐变 | `ocean-gradient` | 深蓝→中蓝→青色→海绿 | 商务汇报、科技发布、数据分析 |
| 复古大地色 | `vintage-earthy` | 深棕→土黄→橄榄绿→米白 | 古典文学、历史文化、传统行业 |
| 华为红 | `huawei-red` | 华为红→深灰→浅灰→白色 | 企业发布、品牌宣传、正式场合 |
| 科技紫 | `tech-purple` | 深紫→紫罗兰→薰衣草→银灰 | AI 产品、创新技术、未来感 |
| 极简黑 | `minimal-black` | 纯黑→深灰→中灰→白色 | 高端发布会、艺术设计、极简风格 |

---

## 主题选择决策树

```
商务/数据/科技/财报？ → Ocean Gradient
古典/文化/传统？     → Vintage Earthy
企业/品牌/正式？     → Huawei Red
AI/创新/未来感？     → Tech Purple
高端/艺术/极简？     → Minimal Black（非数据密集型）
默认               → Ocean Gradient
```

### ⚠️ 禁止组合

| 禁止组合 | 原因 | 建议替代 |
|---------|------|---------|
| Minimal Black + 财报/数据密集型 | 暗色主题图表对比度不足 | Ocean Gradient / Huawei Red |
| Minimal Black + 教育课件 | 暗色背景不适合长时间阅读 | Vintage Earthy / Ocean Gradient |
| Vintage Earthy + 科技/AI | 古典风格与科技感冲突 | Tech Purple / Ocean Gradient |

**数据密集型判断：** PPT 中包含 3+ 个图表组件 → 不应使用 Minimal Black。

### 主题切换规则

1. 用户明确指定 → 优先使用
2. 场景明显匹配 → 推荐并说明理由
3. 场景模糊 → 提供 2-3 个选项
4. 多场景混合 → 选覆盖面最广的（通常 Ocean Gradient）

---

## 1. Ocean Gradient（海洋渐变）

### 配色

```
主色调板:
├─ #003366 (深蓝)    ├─ #0066CC (中蓝)
├─ #00CCCC (青色)    └─ #20B2AA (海绿色)

辅助色板:
├─ Background: #F0F8FF    ├─ Surface: #FFFFFF
├─ Text Primary: #003366  ├─ Text Secondary: #0066CC
└─ Border: #B0E0E6

渐变:
├─ Hero:   135deg, #003366 → #0066CC → #00CCCC
├─ Card:   180deg, #F0F8FF → #FFFFFF
└─ Accent: 90deg, #00CCCC → #20B2AA
```

### 字体

```
中文标题：PingFang SC Semibold    英文标题：Montserrat Semibold
中文正文：PingFang SC Regular     英文正文：Open Sans Regular

字号: 封面 64px / 页面标题 36px / 一级 24px / 二级 20px / 正文 16px / 注释 14px
```

### 装饰元素

- 波浪线条：贝塞尔曲线（stroke: #00CCCC, stroke-width: 2）
- 圆形点缀：半透明圆形（fill: #0066CC, opacity: 0.1）
- 渐变蒙版：底部渐变遮罩

---

## 2. Vintage Earthy（复古大地色）

### 配色

```
主色调板:
├─ #5C4033 (深棕)    ├─ #D2B48C (棕褐)
├─ #808000 (橄榄绿)  └─ #F5F5DC (米白)

辅助色板:
├─ Background: #FAF9F6    ├─ Surface: #FFF8DC
├─ Text Primary: #3E2723  ├─ Text Secondary: #5C4033
└─ Border: #D7CCC8

渐变:
├─ Hero:   135deg, #5C4033 → #8B7355 → #D2B48C
├─ Card:   180deg, #FAF9F6 → #FFF8DC
└─ Accent: 90deg, #808000 → #9ACD32
```

### 字体

```
中文标题：Songti SC Bold          英文标题：Playfair Display Bold
中文正文：Songti SC Regular       英文正文：Lora Regular

字号: 封面 64px / 页面标题 36px / 一级 24px / 二级 20px / 正文 16px / 注释 14px
```

### 装饰元素

- 古典边框：双线边框（stroke: #5C4033, stroke-width: 1）
- 纹理背景：细微纸张纹理（opacity: 0.05）
- 藤蔓花纹：角落装饰花纹（fill: #808000, opacity: 0.3）

---

## 3. Huawei Red（华为红）

### 配色

```
主色调板:
├─ #CF0022 (华为红)  ├─ #333333 (深灰)
├─ #F0F0F0 (浅灰)   └─ #FFFFFF (白色)

辅助色板:
├─ Background: #FFFFFF    ├─ Surface: #F8F8F8
├─ Text Primary: #333333  ├─ Text Secondary: #666666
└─ Border: #E0E0E0

渐变:
├─ Hero:   135deg, #CF0022 → #FF3344
├─ Card:   180deg, #FFFFFF → #F8F8F8
└─ Accent: 90deg, #333333 → #666666
```

### 字体

```
中文标题：Heiti SC Bold            英文标题：Helvetica Neue Bold
中文正文：Heiti SC Regular         英文正文：Helvetica Neue Regular

字号: 封面 64px / 页面标题 36px / 一级 24px / 二级 20px / 正文 16px / 注释 14px
```

### 装饰元素

- 红色色块：几何矩形强调（fill: #CF0022）
- 细线分割：精细分割线（stroke: #E0E0E0, stroke-width: 1）
- 品牌留白：大面积留白突出专业感

---

## 4. Tech Purple（科技紫）

### 配色

```
主色调板:
├─ #4B0082 (深紫)    ├─ #9370DB (紫罗兰)
├─ #E6E6FA (薰衣草)  └─ #C0C0C0 (银灰)

辅助色板:
├─ Background: #0D0D1A    ├─ Surface: #1A1A2E
├─ Text Primary: #FFFFFF  ├─ Text Secondary: #E6E6FA
└─ Border: #4B0082

渐变:
├─ Hero:   135deg, #0D0D1A → #4B0082 → #9370DB
├─ Card:   180deg, #1A1A2E → #2D2D44
└─ Accent: 90deg, #9370DB → #E6E6FA
```

### 字体

```
中文标题：YouYuan Bold             英文标题：Orbitron Bold
中文正文：YouYuan Regular          英文正文：Exo 2 Regular

字号: 封面 64px / 页面标题 36px / 一级 24px / 二级 20px / 正文 16px / 注释 14px
```

### 装饰元素

- 光晕效果：径向渐变光晕（opacity: 0.3）
- 网格线条：科技感网格背景（stroke: #4B0082, stroke-width: 0.5）
- 粒子点缀：小圆形粒子（fill: #E6E6FA, opacity: 0.5）

---

## 5. Minimal Black（极简黑）

### 配色

```
主色调板:
├─ #000000 (纯黑)    ├─ #333333 (深灰)
├─ #808080 (中灰)    └─ #FFFFFF (白色)

辅助色板:
├─ Background: #111111    ├─ Surface: #1E1E1E
├─ Text Primary: #FFFFFF  ├─ Text Secondary: #B0B0B0
└─ Border: #3A3A3A

数据可视化色板（暗色主题专用）:
├─ Chart 1: #FFFFFF       ├─ Chart 2: #B0B0B0
├─ Chart 3: #6B6B6B       ├─ Chart Accent: #E0E0E0
├─ Positive: #4ADE80      ├─ Negative: #F87171
├─ KPI Value: #FFFFFF     ├─ Axis Label: #999999
└─ Grid Line: #2A2A2A

渐变:
├─ Hero:   180deg, #000000 → #1A1A1A
├─ Card:   135deg, #1E1E1E → #2A2A2A
├─ Bar:    180deg, #FFFFFF → #B0B0B0
└─ Accent: 90deg, #E0E0E0 → #FFFFFF
```

### 字体

```
中文标题：PingFang SC Light        英文标题：Helvetica Neue Light
中文正文：PingFang SC Regular      英文正文：Helvetica Neue Regular

字号: 封面 72px / 页面标题 40px / 一级 26px / 二级 22px / 正文 18px / 注释 14px
```

### 装饰元素

- 极简线条：单像素细线（stroke: #333333, stroke-width: 1）
- 负空间：大量留白（留黑）营造高级感
- 对比强调：纯黑白强对比

---

## 暗色主题对比度规则

当使用暗色背景主题（Minimal Black、Tech Purple 等背景色 < #333333）时：

### 文字对比度

| 层级 | 最低亮度 | 推荐色值 |
|------|---------|---------|
| 主要文字 | ≥ 88% | #FFFFFF / #E0E0E0 |
| 次要文字 | ≥ 60% | #999999（禁止 #666666 或更暗） |
| 注释文字 | ≥ 50% | #808080 |

### 图表/数据可视化

- 柱状图：白色→亮灰渐变，禁止深灰色柱子
- 折线图：#FFFFFF 或主题亮色，线宽 ≥ 2.5px
- 饼图扇区：高亮度色彩，禁止深色扇区
- KPI 数值：必须 #FFFFFF，字号 ≥ 36px
- 坐标轴标签：最低 #999999
- 网格线：#2A2A2A ~ #3A3A3A

### 卡片/容器

- 卡片背景：#1E1E1E ~ #2A2A2A（与页面背景 #111111 有区分）
- 卡片边框：#3A3A3A

### 禁止的暗色配色

- ❌ 黑底 + 深灰文字（#000 背景 + #555 文字）
- ❌ 黑底 + 深灰柱子（对比度不足）
- ❌ 深色背景 + 深色扇区
- ❌ 所有图形元素同一灰度

---

## 自定义主题扩展

新增主题需定义：

- 主题 ID（kebab-case）
- 主色调板（4 色）+ 辅助色板（5 色）+ 渐变色板（3 种）
- 字体组合（中英文标题 + 正文）+ 字号规范（6 级）
- 装饰元素（SVG 可绘制的图形）
- 适用场景清单
