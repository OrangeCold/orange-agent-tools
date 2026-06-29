# CLAUDE.md

本文件为 Claude Code（claude.ai/code）在本仓库工作时提供指引。

## 仓库性质

这是一个 **Claude Code 插件市场（plugin marketplace）仓库**，托管并分发 OrangeCloud 个人开发的 Claude Code 插件。它本身不是一个可运行的应用，而是一个插件目录 + 市场清单。

核心约定（务必遵守）：

- 市场清单固定在 **`.claude-plugin/marketplace.json`**（仓库根目录），Claude Code 只在此位置识别市场。
- 所有插件位于 **`plugins/<plugin-name>/`** 下，每个插件有自己的 `.claude-plugin/plugin.json` 清单。
- 新增插件时，**必须同时**：①创建插件目录与 `plugin.json`；②在 `marketplace.json` 的 `plugins` 数组登记一条 `source: "./plugins/<plugin-name>"` 的条目。两者缺一会导致插件不可被发现或加载。

## 关键文件

| 路径 | 作用 |
|------|------|
| `.claude-plugin/marketplace.json` | 市场清单：name / owner / plugins 列表 |
| `plugins/<name>/.claude-plugin/plugin.json` | 单个插件的元数据清单 |
| `plugins/<name>/skills/<skill>/SKILL.md` | 技能定义（模型自动调用） |
| `plugins/<name>/commands/*.md` | 斜杠命令 |
| `plugins/<name>/agents/*.md` | 自定义 agent |
| `plugins/<name>/hooks/hooks.json` | 事件钩子 |
| `plugins/<name>/.mcp.json` | MCP 服务器配置 |

## 插件命名与目录规范

- 插件名、技能名、目录名一律 **kebab-case**（仅小写字母、数字、连字符）。
- 用户调用形式：`/plugin-name:skill-name`，安装形式：`<plugin-name>@orange-agent-tools`。
- SKILL.md 顶部 frontmatter 必须含 `name` 与 `description`；`description` 决定模型何时自动触发，是技能质量的关键。

## 常用操作

```bash
# 本地直接加载某插件（无需安装到市场）
claude --plugin-dir ./plugins/<name>

# 把当前仓库作为本地市场测试
claude plugin marketplace add .
claude plugin install <name>@orange-agent-tools

# 校验
claude plugin validate .                       # 校验市场清单
claude plugin validate ./plugins/<name>        # 校验单个插件

# 修改后热加载（会话内）
/reload-plugins
```

## 工作约定

- 修改插件后，提醒用户运行 `/reload-plugins` 或重启会话生效。
- `${CLAUDE_PLUGIN_ROOT}` 引用插件安装目录；需跨更新保留的状态用 `${CLAUDE_PLUGIN_DATA}`。插件会被复制到缓存目录运行，故不要用 `../` 引用插件外的文件。
- 提交前用 `claude plugin validate .` 自检，避免清单 JSON / frontmatter 语法错误导致整个市场加载失败。
- 参考文档：[创建插件市场](https://code.claude.com/docs/en/plugin-marketplaces) · [创建插件](https://code.claude.com/docs/en/plugins)
