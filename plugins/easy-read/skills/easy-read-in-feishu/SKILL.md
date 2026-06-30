---
name: easy-read-in-feishu
description: 把杂乱的内容用金字塔原理重构为更易读、更易理解的结构化呈现，并输出为飞书云文档。当用户说"把这段内容整理到飞书""做成飞书文档""整理成飞书 wiki""把这份笔记/需求/方案/会议记录做成飞书文档让它好懂"时使用。会先理解内容，遇到不清楚的地方用选择题询问（不篡改、不发挥想象），再用金字塔原理结构化、把适合的部分画成 Mermaid 图表，最后编排 lark-doc 写入飞书文档（图表用飞书原生 mermaid 画板块，可编辑），返回文档链接。
---

# easy-read-in-feishu

把杂乱的内容重构为**更易读、更易理解**的飞书云文档。

核心 = 金字塔原理结构化 + 图表化表达 + 飞书原生块呈现。前 4 步与 easy-read-in-html 完全一致（共享逻辑），差异仅在最后落到飞书文档而非 HTML。

## 为什么这样设计

飞书文档的价值是「原生可协作」——团队成员可直接评论、编辑、@人。所以本 skill 不追求 HTML 级的视觉精致，而是把内容理清（金字塔）、把关系显化（图表），再用飞书原生块（标题层级 / callout / 表格 / 待办 / mermaid 画板）组织成结构清晰、易读易改的文档。视觉服务于理解，飞书原生服务于协作。

## 工作流（6 步）

### 第 1 步：理解输入
读懂用户给的内容，判断类型（需求 / 方案 / 流程 / 系统设计 / 会议记录 / 技术思路 / 计划）、受众、信息完整度。

### 第 2 步：澄清（有疑才问）
**原则：不篡改原文、不发挥想象。** 有歧义 / 缺失 / 多义时，用 `AskUserQuestion` 提选择题（每题 2–4 选项，给推荐项）补全；无歧义跳过。不要为问而问。

### 第 3 步：金字塔结构化
读取 `${CLAUDE_PLUGIN_ROOT}/references/content-structuring.md`，按其准则（结论先行 → 以上统下 → 归类分组 → 逻辑递进）重排为骨架。**只重排、不改写、不补造**；拿不准回第 2 步问。

### 第 4 步：图表化
读取 `${CLAUDE_PLUGIN_ROOT}/references/diagram-guide.md`，识别适合图示的部分，选图表类型（架构 / 流程 / 时序 / 泳道 / 甘特）写 Mermaid 源码。不适合图示的用表格 / 列表。**图表服务于理解。**

### 第 5 步：飞书呈现
读取 `${CLAUDE_PLUGIN_ROOT}/references/feishu-doc-guide.md`，按其映射把金字塔骨架 + 图表规划为飞书块结构（核心结论 → H1 + callout 高亮；支撑 → H2/H3；图表 → mermaid 画板块；对比 → 表格；清单 → 待办；细节 → callout 收拢）。

### 第 6 步：写入飞书
使用 `lark-doc` skill（封装 lark-cli）：
1. **必读**：执行 docs 命令前先 `lark-cli skills read lark-doc` 读嵌入指南，尤其 `references/lark-doc-xml.md`（XML 语法、转义规则）。
2. 创建飞书文档（位置由用户指定或交互确认）：`lark-cli docs +create --api-version v2 --as user --content '<title>…</title>…各块…'`。
3. 按第 5 步结构写入块（标题 / callout / 正文 / 表格 / 待办 / 分割线）。
4. mermaid 图作为飞书原生 `<whiteboard type="mermaid">源码</whiteboard>` 块插入（飞书渲染、可编辑、离线零依赖）。
5. 返回文档 URL 给用户。

身份默认 `--as user`（首次使用前 `lark-cli auth login`）；API 必须 `--api-version v2`。

## 关键原则
- **忠实优先**：不篡改、不补造；拿不准就问（选择题），绝不发挥想象。
- **理解优先于视觉**：先理清逻辑，再组织块。飞书原生块服务于结构与协作。
- **图表服务理解**：能比文字更快讲清关系 / 顺序 / 结构 / 时间的才画图。
- **飞书原生优先**：用原生块（callout / 表格 / 待办 / mermaid 画板）而非截图，保证可编辑可协作。

## 文件结构
```
easy-read/
├── skills/easy-read-in-feishu/SKILL.md   # 本文件
└── references/
    ├── content-structuring.md             # 第 3 步：金字塔准则（与 html 共享）
    ├── diagram-guide.md                   # 第 4 步：图表选型 + Mermaid（与 html 共享）
    └── feishu-doc-guide.md                # 第 5 步：飞书块映射 + 易读原则
```
