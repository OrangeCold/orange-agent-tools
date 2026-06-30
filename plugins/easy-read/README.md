# easy-read

把杂乱内容用金字塔原理重构为更易读、更易理解的结构化呈现。

## Skills

- `easy-read-in-html`：理解内容 → 有疑用选择题澄清（不篡改不发挥）→ 金字塔结构化 → 图表化 → 匹配真实品牌设计风格 → 生成带 Mermaid 图表的单文件自包含 HTML（图表离线可见）。
- `easy-read-in-feishu`：理解内容 → 有疑用选择题澄清（不篡改不发挥）→ 金字塔结构化 → 图表化（Mermaid）→ 飞书原生块呈现 → 编排 lark-doc 写入飞书文档，图表用飞书原生 mermaid 画板块，返回文档链接。

## 设计

两个 skill 共享前置的"内容理解 + 金字塔结构化 + 图表化"逻辑（`references/content-structuring.md`、`references/diagram-guide.md`），仅在输出载体分叉。HTML 美化复用内置的 74 个真实品牌设计系统（移植自 design-to-html）。
