# orange-agent-tools

> OrangeCold 个人开发的 Claude Code 插件市场（plugin marketplace）。

本仓库是一个 [Claude Code 插件市场](https://code.claude.com/docs/en/plugin-marketplaces)，集中托管与分发我自己开发的 Claude Code 插件——包含技能（Skills）、斜杠命令、自定义 agent、hooks 与 MCP 服务器。后续开发的新插件都会逐步集成到此处。

## 目录结构

```
orange-agent-tools/
├── .claude-plugin/
│   └── marketplace.json     # 市场清单：定义市场名、维护者、插件列表
├── plugins/
│   ├── easy-read/           # 易读重构：金字塔结构化 + 图表化 → HTML / 飞书文档
│   │   ├── .claude-plugin/plugin.json
│   │   ├── skills/{easy-read-in-html,easy-read-in-feishu}/SKILL.md
│   │   └── references/      # content-structuring、diagram-guide、design-md×74 等
│   ├── red-book/            # 小红书笔记：真实、不浮夸的笔记编写
│   │   ├── .claude-plugin/plugin.json
│   │   └── skills/note-write/SKILL.md
│   └── example-plugin/      # 示例插件骨架（新插件起点模板）
│       ├── .claude-plugin/plugin.json
│       └── skills/hello/SKILL.md
├── README.md
├── CLAUDE.md                # 给 Claude Code 会话的项目指引
└── LICENSE
```

> 市场清单必须位于仓库根目录的 `.claude-plugin/marketplace.json`，Claude Code 在此位置识别整个市场。

## 添加市场

将本仓库添加为插件市场：

```bash
# GitHub owner/repo 简写（推荐）
/plugin marketplace add OrangeCold/orange-agent-tools

# 或在 CLI 中
claude plugin marketplace add OrangeCold/orange-agent-tools
```

## 安装插件

```bash
# 交互式
/plugin install <plugin-name>@orange-agent-tools

# 命令行
claude plugin install example-plugin@orange-agent-tools
```

更新市场与插件：

```bash
/plugin marketplace update orange-agent-tools   # 拉取市场最新清单
/plugin update                                   # 更新已安装的插件
```

## 插件

### easy-read

把杂乱内容用金字塔原理重构为更易读、更易理解的结构化呈现。

**Skills：**
- `easy-read-in-html`：理解内容 → 有疑用选择题澄清（不篡改不发挥）→ 金字塔结构化 → 图表化 → 匹配真实品牌设计风格 → 生成带 Mermaid 图表的单文件自包含 HTML（图表离线可见）。
- `easy-read-in-feishu`：理解内容 → 有疑用选择题澄清（不篡改不发挥）→ 金字塔结构化 → 图表化（Mermaid）→ 飞书原生块呈现 → 编排 lark-doc 写入飞书文档，图表用飞书原生 mermaid 画板块，返回文档链接。

**设计：** 两个 skill 共享前置的"内容理解 + 金字塔结构化 + 图表化"逻辑，仅在输出载体分叉。HTML 美化复用内置的 74 个真实品牌设计系统（移植自 design-to-html）。

### red-book

把产品、工具或主题写成真实、不浮夸、有场景感的小红书笔记。

**Skills：**
- `note-write`：理清卖点与受众 → 拟标题（≤20 字、多风格备选）→ 功能介绍 → 真实场景（痛点 → 动作 → 效果 三段式）→ 用心细节 + 收尾 → 自检。硬约束：标题 ≤20 字、禁浮夸词（神器 / yyds / 封神…）、第一人称口语、emoji 适度。

**设计：** 真人分享口吻优先于硬广——用具体场景讲功能，而非堆砌功能清单。

## 添加自己的插件（开发流程）

1. **创建插件目录**：在 `plugins/` 下新建 `<your-plugin>/`，子目录名用 kebab-case。
2. **写清单**：创建 `plugins/<your-plugin>/.claude-plugin/plugin.json`（参考 `example-plugin`）。
3. **添加能力**：按需加入 `skills/`、`commands/`、`agents/`、`hooks/hooks.json`、`.mcp.json` 等。
4. **登记到市场**：在 `.claude-plugin/marketplace.json` 的 `plugins` 数组追加一条目：

   ```json
   {
     "name": "your-plugin",
     "source": "./plugins/your-plugin",
     "description": "一句话说明这个插件做什么",
     "version": "0.1.0",
     "author": { "name": "OrangeCold" },
     "license": "MIT"
   }
   ```

5. **本地测试**：

   ```bash
   claude --plugin-dir ./plugins/your-plugin          # 直接加载，无需安装
   # 或把整个市场作为本地源测试
   /plugin marketplace add .
   /plugin install your-plugin@orange-agent-tools
   ```

6. **校验**：`claude plugin validate .`（校验市场清单）或 `claude plugin validate ./plugins/your-plugin`（校验单个插件）。
7. **提交推送**：git commit & push 后，已添加市场的用户用 `/plugin marketplace update` 即可拉取。

## 版本策略

- 默认不写 `version`：每个 git commit 自动作为一个版本（适合内部、活跃开发）。
- 写了 `version`：插件被固定到该字符串，仅当 `version` 变化时用户才收到更新。

## License

[MIT](./LICENSE)
