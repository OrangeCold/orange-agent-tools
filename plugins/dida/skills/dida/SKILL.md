---
name: dida
description: 通过 `dida` CLI（滴答清单命令行工具）管理用户的滴答清单 / dida365 账户：任务、清单、习惯、专注（番茄钟）、标签、倒数日。当用户提到滴答清单、dida365、待办、任务、待办事项、加任务/建任务/记一下这事、今天有什么要做、完成/删掉那个任务、改一下任务、习惯打卡、番茄钟/专注计时、倒数日/纪念日，或想在终端里查看/创建/更新/完成这些内容时使用。即使用户没说“用 dida”或“用命令行”，只要意图是操作滴答清单里的任务/习惯/专注，就应触发本 skill——它会指导如何把用户的自然语言翻译成正确的 `dida` 命令，含清单 ID 反查、--json 取结构化数据、写操作前与用户确认等关键细节。
---

# dida

用 `dida` CLI 管理用户的滴答清单（dida365）账户。

`dida` 是封装 DIDA Open API v1 的命令行工具，能创建/查询/更新任务、清单、习惯、专注记录、标签、倒数日。本 skill 的职责是：把用户的自然语言意图，翻译成**正确、可执行**的 `dida` 命令。

## 三条命脉（决定命令能不能跑对）

理解这三条，比记住任何具体命令都重要。

### 1. 名称永远要先反查成 ID

`dida` 的写操作和大多数查询都要 `projectId` / `taskId` / `habitId` 等 ID，而用户永远只说名称（“在工作清单加个任务”、“完成那个买牛奶的任务”）。

所以**第一步永远是反查 ID**，绝不凭名称猜测 ID：

```bash
dida project list --json     # 清单名 → projectId
dida habit list --json       # 习惯名 → habitId
dida tag list --json         # 标签名（标签用 name，不用 id）
```

任务没有“全局列表”接口。要在一个清单里找任务，用 `dida project data <projectId> --json`，返回里的 `tasks` 数组就是该清单未完成的任务，从中拿到 `taskId`。

> 反查结果可能重名（多个叫“工作”的清单、多个含“买牛奶”的任务）。重名或找不到时，回去问用户澄清，不要替用户瞎选一个。

### 2. 给 agent 自己看的数据，一律加 `--json`

任何命令都能加 `--json`，输出原始 API JSON（camelCase 字段）。**只要你还要继续处理这份数据（找 ID、解析字段、判断结果），就必须加 `--json`**——解析人类可读的表格是脆弱的，表格格式随时可能变。

```bash
dida project list --json                      # ✓ 结构化，可解析
dida task filter --projects <id> --json       # ✓
```

只有当数据是直接呈现给用户“看一眼就好”的最终结果时，才可以省略 `--json`。

### 3. 写操作前，先跟用户确认

创建、更新、完成、删除任务/清单/习惯都是**会真实改动用户滴答清单账户**的写操作。滴答清单是用户的私人待办系统，误操作会污染真实数据。

- 用户在当前对话里**已经明确、具体地**要求了某个操作（“帮我加个任务叫买牛奶”），可以直接执行。
- 意图模糊时先澄清：“你是想新建一个任务，还是更新已有的？”
- **删除尤其谨慎**：先确认目标 ID 对应的确实是用户说的那个对象，再删。
- 批量操作（一次改多条）先列出计划让用户过目。

## 前置：确认已登录

第一次接触 dida 时，先确认登录状态（只读、无副作用）：

```bash
dida auth status     # ✓ 已登录 / ✗ 未登录
```

未登录时引导用户：
- `dida auth login` —— OAuth 浏览器登录（推荐，PKCE，无需 secret）
- `dida auth token <token>` —— 无浏览器环境用；token 在滴答清单网页「设置 → 账户与安全 → API 口令」获取

## 核心工作流：操作任务（最高频）

用户绝大多数诉求落在四类：**加任务、看任务、改任务、完成任务**。

### 加任务

```bash
# 1. 反查清单 ID
dida project list --json

# 2. 创建（projectId 从上一步拿到）
dida task create --title "买牛奶" --project <projectId>
dida task create --title "周三开会" --project <projectId> \
  --priority 5 --due-date "2026-07-15T09:00:00+0800"
dida task create --title "复盘" --project <projectId> --tags 工作,紧急
```

用户没指定清单时，先问放哪个清单（用 `project list` 的结果让用户选），不要默认塞进某个清单。`--project` 是必填项。

### 看任务

```bash
# 看某个清单里有哪些未完成任务（含 taskId）
dida project data <projectId> --json

# 跨清单按条件筛
dida task filter --projects <pid1>,<pid2> --priority 3,5 --status 0 --json

# 看一段时间内已完成的
dida task completed --projects <projectId> \
  --start-date "2026-07-01T00:00:00+0800" --end-date "2026-07-07T23:59:59+0800" --json
```

用户说“今天有什么任务”——用 `task filter` 配合今天的起止时间；或先 `project list` 看有哪些清单，再逐个 `project data`。

### 改任务（⚠️ 冗余参数陷阱）

`task update` 的参数设计有冗余，**容易写错**：

```bash
# 必须同时给：位置参数 <taskId>、--id <taskId>、--project <projectId>，三者缺一不可
dida task update <taskId> --id <taskId> --project <projectId> --title "新标题"
```

`<taskId>` 与 `--id` 要一致；漏掉 `--id` 或 `--project` 都会失败。改之前先用 `project data` 反查 taskId。

```bash
# 改优先级 / 截止
dida task update <taskId> --id <taskId> --project <projectId> --priority 5 --due-date "..."
# 加标签
dida task update <taskId> --id <taskId> --project <projectId> --tags 工作,紧急
# 设为子任务
dida task update <taskId> --id <taskId> --project <projectId> --parent-id <parentTaskId>
# 取消父子关联
dida task update <taskId> --id <taskId> --project <projectId> --parent-id null
```

### 完成任务

```bash
dida task complete <projectId> <taskId>
```

## 其他域

### 习惯（habit）

```bash
dida habit list --json                                              # → habitId
dida habit create --name "每天喝水" --repeat "RRULE:FREQ=DAILY;INTERVAL=1" --goal 8 --unit 杯
dida habit checkin <habitId> --stamp 20260711 --value 1 --goal 1    # 打卡
dida habit checkins --habits <habitId> --from 20260701 --to 20260731 --json
```

⚠️ `stamp` / `from` / `to` 都是 **YYYYMMDD** 纯数字（如 `20260711`），不是 ISO 格式。

### 专注 / 番茄钟（focus）

```bash
dida focus list --from "2026-07-01T00:00:00+0800" --to "2026-07-07T23:59:59+0800" --type pomodoro --json
dida focus create --type pomodoro --task-id <taskId> \
  --start-time "2026-07-11T09:00:00+0800" --end-time "2026-07-11T09:25:00+0800" --duration 1500
```

⚠️ `focus list` 时间范围**最大 30 天**，超过会失败。`--type` 通常是 `pomodoro`。

### 标签（tag）与倒数日（countdown）

```bash
dida tag list --json
dida tag create --name urgent --label urgent
dida countdown list --json
```

## 易踩的坑（速查）

| 坑 | 正解 |
|----|------|
| 把用户说的名称直接当 ID 用 | 先 `project list --json` / `project data --json` 反查 |
| 解析 dida 的表格输出 | 给自己看的数据加 `--json` |
| `task update` 报错 | 补齐 `<taskId>` + `--id <taskId>` + `--project <projectId>` 三件套 |
| priority / status 乱填 | priority：`0`无 `1`低 `3`中 `5`高；status：`0`未完成 `-1`已放弃 `2`已完成 |
| 日期格式 | 任务用 ISO 8601 带时区 `2026-07-15T09:00:00+0800`；habit 打卡用 `YYYYMMDD` |
| `focus list` 超范围 | 时间跨度 ≤ 30 天 |
| habit 重复规则写错 | 用 `RRULE:FREQ=DAILY;INTERVAL=1` 这类标准规则，详见 references/fields.md |

## 需要完整命令清单或字段细节时

- **所有命令的完整用法与示例** → 读 `references/commands.md`
- **JSON 字段含义、priority/status/提醒/重复规则等格式** → 读 `references/fields.md`

只在需要时再去读这两个文件，避免一次性把上下文塞满。
