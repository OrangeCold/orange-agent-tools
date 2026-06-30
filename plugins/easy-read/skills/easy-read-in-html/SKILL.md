---
name: easy-read-in-html
description: 把杂乱的内容用金字塔原理重构为更易读、更易理解的结构化呈现，并输出带图表（架构图/流程图/时序图/泳道图/甘特图）的单文件 HTML。当用户说"把这段内容整理一下""帮我重构得更易读""把这份笔记/需求/方案/会议记录做成好懂的网页""这个内容有点乱，帮我理清楚并做成 HTML"时使用。会先理解内容，遇到不清楚的地方用选择题询问（不篡改、不发挥想象），再用金字塔原理结构化、把适合的部分画成 Mermaid 图表，最后用内置的 74 个真实品牌设计系统渲染成美观 HTML。
---

# easy-read-in-html

把杂乱的内容重构为**更易读、更易理解**的结构化 HTML。

核心 = 金字塔原理结构化 + 图表化表达 + 真实品牌设计系统渲染。内置 74 个从真实网站提取的设计系统（颜色/字体/间距/组件 token + Do's & Don'ts），图表用 Mermaid（内联，离线可见）。

## 为什么这样设计

让人「看懂」比让人「看到」更难。杂乱内容的真正问题往往不是丑，而是逻辑没理清——结论埋在细节里、层次混乱、关系靠脑补。所以本 skill 的质量命脉是**先用金字塔原理把内容理清**，再用图表把"关系/顺序/结构/时间"显化，最后才落到品牌化的视觉。视觉服务于理解，不是替代理解。

## 工作流（6 步）

### 第 1 步：理解输入

读懂用户给的内容，判断：
- **类型**：需求 / 方案 / 流程 / 系统设计 / 会议记录 / 技术思路 / 计划 / 概念解释…
- **受众与明暗偏好**（用户没说就推断）
- **信息完整度**：哪些完整、哪些有歧义或缺失

### 第 2 步：澄清（有疑才问）

**原则：不篡改原文、不发挥想象。** 凡是要写进结论但原文支撑不了的，一律问。
- 内容有歧义/缺失/多义时，用 `AskUserQuestion` 提**选择题**（每题 2–4 选项，给推荐项）让用户补全。
- 无歧义时**跳过**此步，直接往下。不要为问而问。

### 第 3 步：金字塔结构化

读取 `${CLAUDE_PLUGIN_ROOT}/references/content-structuring.md`，按其准则（结论先行 → 以上统下 → 归类分组 → 逻辑递进）把内容重排为骨架。**只重排、不改写、不补造**；拿不准的回第 2 步问用户。

### 第 4 步：图表化

读取 `${CLAUDE_PLUGIN_ROOT}/references/diagram-guide.md`，识别骨架中适合图示的部分（关系/顺序/结构/时间），选对应图表类型（架构图/流程图/时序图/泳道图/甘特图）写 Mermaid 源码。不适合图示的（纯结论/对比/清单）用卡片或表格。**图表服务于理解，不为画图而画图。**

### 第 5 步：匹配设计风格

读取 `${CLAUDE_PLUGIN_ROOT}/references/style-index.md`（**匹配只靠它，不要直接翻 design-md/**），沿四维语义匹配 1–3 个候选：内容类型 / 调性氛围 / 明暗偏好 / 行业线索。
- 唯一命中 → 直接用，告诉用户选了哪个、为什么。
- 多个都说得通 → 用 `AskUserQuestion` 单选，每选项带「调性 + 主色 + 明暗 + 适合理由」。
- 用户直接点名品牌 → 锁定该风格。

### 第 6 步：生成 HTML

读取选中的 `${CLAUDE_PLUGIN_ROOT}/references/design-md/<slug>.md`（**只读这一个**），再读 `${CLAUDE_PLUGIN_ROOT}/references/html-generation-guide.md` 与 `${CLAUDE_PLUGIN_ROOT}/references/typography-system.md`（内容含中文时后者必读），按规范生成**单文件自包含 HTML**：
- 金字塔骨架映射到页面结构：核心结论做 hero 大标题，支撑要点做章节，细节做正文/卡片/图表。
- 图表用 Mermaid；mermaid.min.js 用「占位符 + python3 注入」内联（见 html-generation-guide 的 Mermaid 章节），保证离线可见。
- 图表配色用选中品牌的强调色（`themeVariables`）。
- CSS 全内联；字体走 Google Fonts + 完整 fallback 栈（含 PingFang SC 等）。
- 颜色/字号/字距/圆角/间距一律用 design-md 真实 token；中文按 typography-system 适配 5 个排版维度。
- 逐条遵守该风格 Do's & Don'ts。
- 过一遍 html-generation-guide 的自检清单再交付。

输出单文件 `.html`，保存到用户指定位置（未指定则当前工作目录），告知绝对路径并提示浏览器打开预览。

## 关键原则

- **忠实优先**：不篡改、不补造；拿不准就问（选择题），绝不发挥想象。
- **理解优先于视觉**：先理清逻辑，再美化。视觉服务于理解。
- **图表服务理解**：能比文字更快讲清关系/顺序/结构/时间的才画图；否则用文字/表格。
- **忠于品牌 token**：颜色/圆角/间距/组件用 design-md 真实值，不自创近似；Do's & Don'ts 是硬约束。
- **按需加载**：只读匹配到的那一个 design-md。

## 文件结构

```
easy-read/
├── skills/easy-read-in-html/SKILL.md   # 本文件（6 步工作流）
└── references/
    ├── content-structuring.md           # 第 3 步：金字塔准则
    ├── diagram-guide.md                 # 第 4 步：图表选型 + Mermaid
    ├── style-index.md                   # 第 5 步：74 风格索引
    ├── html-generation-guide.md         # 第 6 步：HTML 规范（含 Mermaid 渲染/内联/配色）
    ├── typography-system.md             # 第 6 步：CJK 排版适配
    ├── mermaid.min.js                   # 第 6 步：图表库（内联用）
    └── design-md/                       # 74 个品牌设计系统（只读选中的那个）
```
