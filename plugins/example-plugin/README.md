# example-plugin

`orange-agent-tools` 市场中的示例插件骨架，用于演示标准插件结构，并作为开发新插件的起点模板。

## 结构

```
example-plugin/
├── .claude-plugin/
│   └── plugin.json     # 插件清单（元数据）
├── skills/
│   └── hello/
│       └── SKILL.md    # 示例技能
└── README.md
```

## 本地测试

无需安装到市场即可直接加载该插件：

```bash
# 在仓库根目录执行
claude --plugin-dir ./plugins/example-plugin
```

修改后执行 `/reload-plugins` 即可热加载，无需重启。

## 可以扩展的能力

| 目录 / 文件 | 作用 |
|-------------|------|
| `skills/` | 技能（模型根据上下文自动调用） |
| `commands/` | 斜杠命令（扁平 `.md` 文件） |
| `agents/` | 自定义 agent 定义 |
| `hooks/hooks.json` | 事件钩子 |
| `.mcp.json` | MCP 服务器配置 |
| `.lsp.json` | LSP 代码智能 |
| `monitors/monitors.json` | 后台监听器 |

> `${CLAUDE_PLUGIN_ROOT}` 变量可在 hooks / MCP 配置中引用插件安装目录的文件；需跨更新保留的状态请用 `${CLAUDE_PLUGIN_DATA}`。

完整说明见 [Claude Code 插件文档](https://code.claude.com/docs/en/plugins)。
