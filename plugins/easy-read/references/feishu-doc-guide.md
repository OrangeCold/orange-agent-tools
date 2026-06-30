# 飞书文档呈现指南

> easy-read-in-feishu 第 5 步用。把金字塔骨架 + 图表规划映射为飞书文档的**原生块结构**。飞书无 CSS——「易读」靠块的组织（callout 配色、表格对齐、标题层级、画板图表），不靠样式表。

## 金字塔 → 飞书块映射

| 内容 | 飞书块 | XML 写法 |
|---|---|---|
| 文档标题 | `<title>` | `<title>文档标题</title>`（每篇唯一） |
| 核心结论 | H1 + callout 高亮 | `<h1>核心结论</h1>` 后接 `<callout emoji="💡" background-color="light-blue"><p>结论</p></callout>` |
| 支撑要点 | H2 / H3 章节 | `<h2>支撑要点</h2>` |
| 图表 | whiteboard mermaid | `<whiteboard type="mermaid">flowchart LR\nA-->B</whiteboard>` |
| 对比 / 多维属性 | 表格 | `<table><thead><tr><th>…</th></tr></thead><tbody><tr><td>…</td></tr></tbody></table>` |
| 清单 / 待办 | 待办列表 | `<checkbox done="false">事项</checkbox>` |
| 长细节 / 次要说明 | callout 收拢（飞书无折叠块） | `<callout emoji="📝"><p>补充细节</p></callout>` |
| 章节分隔 | 分割线 + emoji 标题 | `<hr/>` + `<h2>📌 章节名</h2>` |
| 分栏对比 | grid | `<grid><column width-ratio="0.5">…</column><column width-ratio="0.5">…</column></grid>` |

## 图表方案

**用飞书原生 whiteboard mermaid 块**（已实测可行）：

- 把 `${CLAUDE_PLUGIN_ROOT}/references/diagram-guide.md` 选定图表的 mermaid 源码原样写入 `<whiteboard type="mermaid">源码</whiteboard>`。
- 飞书原生渲染（架构图 / 流程图 / 时序图 / 泳道图 / 甘特图均支持）、**可编辑**、零额外工具链。
- mermaid 源码内的换行用**真实换行**（whiteboard 内特殊处理，不要转成 `<br/>`）。
- 选型与写法仍遵循 diagram-guide.md——本文件只规定「用飞书 whiteboard 块承载」，不重复选型逻辑。
- 极少数需要精确自由布局的复杂图，可备选 `<whiteboard type="blank">` + lark-whiteboard skill；但绝大多数图直接 `type="mermaid"` 即可。

## 飞书「易读」原则

- **结论先行**：核心结论置顶 + callout 高亮（emoji + 配色），读者第一眼看到。
- **层次靠标题**：H1→H2→H3 体现金字塔层级；不靠字号/颜色替代结构。
- **图表服务理解**：同 diagram-guide 准则——能比文字更快讲清关系/顺序/结构/时间的才画图；纯结论/对比/清单用 callout/表格/列表。
- **降噪**：对比表格化、清单待办化、次要细节用 callout 收拢（飞书无原生折叠块，callout 是最近替代）。
- **配色克制**：callout 配色只用于强调（结论=light-blue、风险/警告=light-red、提示=light-yellow、成功=light-green），不要满篇彩色。
- **emoji 锚点**：章节标题前加 emoji（📌💡✅⚠️）便于快速定位。
- **忠实**：只重排不补造，拿不准回澄清步骤问用户。

## 第 6 步：写入飞书（lark-doc 编排要点）

用 `lark-doc` skill（封装 lark-cli）：

```bash
# 创建文档（一次性写入完整 XML 结构）
lark-cli docs +create --api-version v2 --as user --content '<title>标题</title>…各块…'

# 或追加到已有文档
lark-cli docs +update --api-version v2 --as user --doc "文档URL或token" --command append --content '<…>'
```

- **必读**（lark-cli 强制）：执行 docs 命令前先 `lark-cli skills read lark-doc`，尤其 `references/lark-doc-xml.md`（XML 语法）——块写法、转义规则（标签本身不转义、文本内 `<>&` 转义）以它为准。
- **身份**默认 `--as user`；首次使用前 `lark-cli auth login`。
- **API 版本**：必须 `--api-version v2`。
- 完成后返回文档 URL 给用户。
