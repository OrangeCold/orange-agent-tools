# HTML 生成规范

选定风格后，读取 `references/design-md/<slug>.md`，按本规范把用户内容生成为**单文件自包含 HTML**。

## 硬性规则

1. **单文件**：所有 CSS 内联在一个 `<style>`，零本地外部文件依赖。保存为一个 `.html`，双击即可在浏览器打开、可直接分享。
2. **字体**：查 DESIGN.md 的「Note on Font Substitutes / Font Substitutes」部分，引入它推荐的免费替代（通常是 Inter / Geist / IBM Plex Mono / Space Grotesk / JetBrains Mono 等）的 Google Fonts `<link>`，并写**完整 fallback 字体栈**保证离线降级。不要硬塞专有字体名（Linear Display、SoDoSans 等），否则会回退失败。
3. **颜色**：直接用 DESIGN.md frontmatter `colors:` 里的真实 hex，**不要自己编近似色**。
4. **尺度**：字号 / 字重 / 行高 / 字距用 `typography:` token；圆角用 `rounded:`；间距用 `spacing:` scale。
5. **组件形态**：按钮 / 卡片 / 输入 / 导航等按 `components:` 定义渲染，包括 hover/pressed/focused 等状态。
6. **Do's & Don'ts 必读必守**：这是与「通用漂亮页面」的核心区别。逐条对照，违反任一条 Don't 的元素必须移除——哪怕它「看起来更美」。
7. **字体与排版可读性（CJK 适配）**：DESIGN.md 的字号/行高/字距/字重 token 全是为英文优化的。**当内容含中文（中日韩）时，必须读取 `references/typography-system.md` 并按其规则适配**——正文 ≥16px、行高 ≥1.7、标题禁负字距、正文禁 <400 字重、字体栈补中文字体。颜色/圆角/间距仍 100% 用原 token，只有这 5 个排版维度在 CJK 时覆盖。纯英文内容不动。

## 字体引入示例

```html
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&family=JetBrains+Mono&display=swap" rel="stylesheet">
<style>
  body { font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif; }
  code, .mono { font-family: 'JetBrains Mono', ui-monospace, 'SF Mono', Menlo, monospace; }
</style>
```

## 页面结构（按内容智能选择）

根据用户内容类型，用该风格的组件拼装结构。常见映射：

- **产品落地页**：`top-nav` + `hero` + `feature grid` + 截图/演示区 + `CTA banner` + `footer`
- **文章 / 周报 / 长文**：`top-nav` + 标题区（eyebrow + 大标题 + meta）+ 正文（遵循该风格排版节奏与行高）+ 作者/日期
- **简历 / 作品集**：`top-nav` + intro + 经历/作品网格 + 联系区
- **数据 / 看板类**：导航 + 指标卡网格 + 图表区
- **通用不确定时**：`hero` + 内容区 + `footer` 为骨架，按内容块增减

结构服务于内容，不要为了塞满组件而硬造无关区块。

## 明暗与氛围

DESIGN.md 定义的是 light 就用 light，是 dark 就用 dark——**不要擅自加主题切换开关**。暗色系的留白靠 surface 层级（canvas → surface-1 → surface-2）而非空隙；亮色系靠真实留白。氛围（摄影驱动 / 产品截图当主角 / 渐变 / 单色禁欲 等）要忠实还原该风格的「标志特征」。

## 响应式

按 DESIGN.md 的「Responsive Behavior」部分加媒体查询：断点、卡片网格列数坍缩、导航转汉堡菜单、大标题在移动端缩放。触控目标 ≥40–44px。

## 生成后自检清单

逐条过一遍再交付：

- [ ] 是否单文件、双击即可在浏览器打开
- [ ] 所有颜色是否来自 DESIGN.md（没有自创近似色）
- [ ] 是否违反该风格的任一条 Don't
- [ ] 是否呈现了该风格的「标志特征」（索引中的标志特征列）
- [ ] 字体是否用了推荐的免费替代 + fallback 栈
- [ ] 响应式断点是否符合 DESIGN.md
- [ ] 圆角 / 间距 / 字距是否用了 token 而非随意值
- [ ] **若内容含中文**：正文是否 ≥16px、caption 是否 ≥14px、行高是否 ≥1.7
- [ ] **若内容含中文**：中文标题是否避开了 DESIGN.md 的负字距、正文是否避免了 <400 细字重
- [ ] **若内容含中文**：字体栈是否补了 PingFang SC / Microsoft YaHei 等中文字体；移动端正文是否未被缩小

## 输出方式

把生成的 HTML 写成一个 `.html` 文件，保存到用户指定位置（未指定则存到当前工作目录，文件名与内容相关），告知用户绝对路径，并提示可直接用浏览器打开预览。若用户只想看代码不想要文件，也可直接在回复中给出完整 HTML。

---

## Mermaid 图表渲染（easy-read 补充）

easy-read 会用 Mermaid 把适合图示的内容画成架构图/流程图/时序图/泳道图/甘特图。具体选型与写法见 `diagram-guide.md`；本节规定渲染与内联规范。

### Mermaid 容器

正文里每个图用 `<pre class="mermaid">` 包裹源码：

```html
<pre class="mermaid">
flowchart LR
  A[用户输入] --> B[金字塔结构化]
  B --> C[图表化]
  C --> D[HTML]
</pre>
```

### 内联 mermaid.min.js（离线可见的关键）

单文件自包含要求 Mermaid 库**内联**而非走 CDN。但 mermaid.min.js 约 2–3MB，**不要**把它 Read 进上下文再粘贴。做法：

1. 生成 HTML 时，在 `<head>` 内放一个占位标记，单独成行：
   ```html
   <script>/*__MERMAID_JS__*/</script>
   ```
2. 紧接其后放初始化脚本（非占位，直接写）：
   ```html
   <script>
     mermaid.initialize({ startOnLoad: true, theme: 'base', themeVariables: { /* 见下：品牌配色 */ } });
   </script>
   ```
3. HTML 写盘后，用 python3 把 mermaid.min.js 内容注入占位（最稳，无转义问题）：
   ```bash
   python3 -c "
   import pathlib, os
   root = os.environ.get('CLAUDE_PLUGIN_ROOT', 'plugins/easy-read')
   html_path = '<生成的html路径>'
   html = pathlib.Path(html_path).read_text()
   js = pathlib.Path(root + '/references/mermaid.min.js').read_text()
   pathlib.Path(html_path).write_text(html.replace('/*__MERMAID_JS__*/', js))
   "
   ```

### 图表配色跟随品牌

用选中 design-md 的强调色配置 `themeVariables`，让图表与品牌一致（如 Linear 用薰衣草蓝 `#5e6ad2`）。常见键：

```js
themeVariables: {
  primaryColor: '<品牌强调色>',
  primaryTextColor: '#fff',
  primaryBorderColor: '<品牌强调色>',
  lineColor: '<品牌中性色>',
  background: '<页面底色>'
}
```

### Mermaid 渲染自检

- [ ] 每个 `<pre class="mermaid">` 源码语法正确（无渲染报错）
- [ ] mermaid.min.js 已用占位注入方式内联（HTML 断网打开图表仍可见）
- [ ] 图表配色用了选中品牌的强调色
- [ ] 图表节点文字是中文时字号可读（必要时调 `themeVariables.fontSize`）
