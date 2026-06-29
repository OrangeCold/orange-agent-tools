---
name: hello
description: 最小示例技能，用于问候并验证插件已正确加载。可作为编写新 SKILL.md 的起点模板。
---

# Hello 示例技能

这是 `example-plugin` 内置的最小示例技能，仅用于：

1. 验证插件已正确安装并能被加载
2. 作为编写新 SKILL.md 的起点模板

## 用法

安装插件后，通过带命名空间的方式调用：

```
/example-plugin:hello
```

## 如何编写一个新技能

1. 在插件的 `skills/` 目录下新建子目录，目录名即技能名（kebab-case，仅小写字母、数字、连字符）
2. 在子目录中创建 `SKILL.md`，顶部使用 YAML frontmatter 声明 `name` 和 `description`
3. `description` 决定 Claude 何时自动调用该技能，务必写清楚触发场景与边界
4. 正文是该技能被调用时要遵循的指令

技能是「模型主动调用」的——Claude 会根据任务上下文自动判断是否使用，而不是靠用户手动触发。因此 `description` 写得越精准，触发就越可靠。
