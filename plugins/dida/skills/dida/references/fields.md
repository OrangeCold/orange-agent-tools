# dida CLI JSON 字段与格式参考

`--json` 输出的字段含义、以及 `create` / `update` 写入字段时的取值规范。本文件是 SKILL.md 的展开参考——需要确认某个字段含义或格式（如 priority 编码、reminders、repeatFlag）时才读。

字段名一律 **camelCase**，与 `task create` / `task update` 的长选项对应。

## 任务（Task）

出现在：`task get … --json`、带 `--json` 的 `task create` / `task update`、`task completed` / `task filter` 的每一行，以及 `project data … --json` 的 `tasks` 数组。

| 字段 | 含义 | CLI（create / update） |
|------|------|------------------------|
| `id` | 任务 id（ObjectId 十六进制） | 仅 `update`：`<taskId>` 与 `--id` |
| `projectId` | 所属清单 | `--project` |
| `sortOrder` | 列表排序 | `--sort-order` |
| `title` | 标题，宜短；长文用 `content` | `--title` |
| `content` | 普通/笔记类任务的正文 | `--content` |
| `desc` | 清单类任务的说明 | `--desc` |
| `startDate` / `dueDate` | 时间；两者不同表示一段区间 | `--start-date`、`--due-date` |
| `timeZone` | 时区 | `--time-zone` |
| `isAllDay` | 是否全天 | `--all-day` |
| `priority` | `0` 无，`1` 低，`3` 中，`5` 高 | `--priority` |
| `reminders` | 提醒触发串（格式见下） | `--reminders` |
| `repeatFlag` | 重复规则：`RRULE` 或 `ERULE`（格式见下） | `--repeat` |
| `completedTime` | 完成时间 | — |
| `status` | `0` 未完成，`-1` 已放弃，`2` 已完成 | — |
| `items` | 检查事项 | `--items`（见下） |
| `tags` | 标签 | `--tags`（逗号分隔） |
| `columnId` / `columnName` | 分组 | — |
| `parentId` / `childIds` | 父子任务关系 | `--parent-id`（`null`/`none` 取消关联） |
| `focusSummaries` | 任务专注汇总 | `--estimated-duration`、`--estimated-pomo` |
| `assignor` | 分配相关信息 | — |
| `etag` | 服务端乐观锁 | — |
| `kind` | 如 `TASK`、`NOTE`、`CHECKLIST` | — |

### reminders 格式

数组每一项须符合：

`TRIGGER(;RELATED=START|END)?:(-)?P[nY][nM][nW][nD][T[nH][nM][nS]]`

- **TRIGGER** — 必填前缀。
- **`;RELATED=START` / `;RELATED=END`** — 可选；相对任务开始或结束时间。
- **冒号后的 `-`** — 可选；表示在参考时间**之前**触发；不写则**之后**。
- **`P` 及后续片段** — 时长；`nY nM nW nD` 为年月周日；`T` 分隔日期与时间；`nH nM nS` 为时分秒（类 ISO-8601 时长，带方向与参考点）。

| 字符串 | 含义 |
|--------|------|
| `TRIGGER:-PT60M` | 参考时间点前 60 分钟 |
| `TRIGGER:-P1DT2H` | 参考时间点前 1 天 2 小时 |
| `TRIGGER;RELATED=END:-PT15M` | **结束时间**前 15 分钟 |
| `TRIGGER:PT0S` | 准时（恰在参考时刻） |

### repeatFlag 格式

须为一条合法的重复规则字符串：

- **`RRULE`** — 标准重复（类 RFC 规则）。
- **`ERULE`** — 自定义或高级重复。

**不要**在同一条 `repeatFlag` 里混用 `RRULE` 与 `ERULE`。

示例：

- `RRULE:FREQ=DAILY`
- `RRULE:FREQ=WEEKLY;BYDAY=MO,WE`
- `RRULE:FREQ=DAILY;INTERVAL=1`（每 1 天，habit 常用）
- `ERULE:NAME=CUSTOM;BYDATE=20260325,20260330`

### 检查事项（items[]）

清单型任务中，`items` 数组每个元素是一条检查事项：

| 字段 | 含义 |
|------|------|
| `id` | 检查事项 id（ObjectId 十六进制） |
| `status` | `0` 未完成，`1` 已完成 |
| `title` | 检查事项文案 |
| `sortOrder` | 检查事项之间的排序 |
| `startDate`、`isAllDay`、`timeZone`、`completedTime` | 可选时间与完成信息 |

### 专注汇总（focusSummaries[]）

可编辑（通过 `task update`）：

| 字段 | 含义 | CLI 参数 |
|------|------|----------|
| `estimatedDuration` | 预计专注时长（**秒**） | `--estimated-duration` |
| `estimatedPomo` | 预计番茄数，最大 `60` | `--estimated-pomo` |

只读（接口返回）：

| 字段 | 含义 |
|------|------|
| `pomoCount` | 已完成番茄数 |
| `pomoDuration` | 番茄专注时长（**秒**） |
| `stopwatchDuration` | 正计时专注时长（**秒**） |

## 清单（Project）

`project list` / `project get --json` 的对象，以及 `project data --json` 里的 `project`。`project create` / `update` 写入同类字段。

| 字段 | 含义 | CLI |
|------|------|-----|
| `id` | 清单 id（ObjectId 十六进制） | `get`、`update`、`data`、`delete` 的参数 |
| `name` | 名称 | `--name` |
| `color` | 颜色 | `--color` |
| `sortOrder` | 侧栏顺序 | — |
| `closed` | 是否关闭/归档 | — |
| `groupId` | 文件夹 id | — |
| `viewMode` | `list`、`kanban`、`timeline` | `--view-mode` |
| `permission` | `read`、`comment`、`write` | — |
| `kind` | `TASK` 或 `NOTE` | `--kind` |

### 分组（columns[]）

`project data … --json` 中 `columns` 数组的元素：

| 字段 | 含义 |
|------|------|
| `id` | 列 id（ObjectId 十六进制） |
| `projectId` | 所属清单 |
| `name` | 列标题 |
| `sortOrder` | 看板上的顺序 |

### project data 根对象

| 字段 | 含义 |
|------|------|
| `project` | 一个**清单**对象 |
| `tasks` | 该清单下未完成的**任务** |
| `columns` | 该清单的**分组** |

## 请求体字段

### task completed

对应 `POST /task/completed`：

| JSON 字段 | 命令行 |
|-----------|--------|
| `projectIds` | `--projects`（逗号分隔） |
| `startDate` / `endDate` | `--start-date`、`--end-date`（ISO 8601） |

### task filter

对应 `POST /task/filter`：

| JSON 字段 | 命令行 |
|-----------|--------|
| `projectIds` | `--projects` |
| `startDate` / `endDate` | `--start-date`、`--end-date` |
| `priority` | `--priority`（逗号分隔：`0`、`1`、`3`、`5`） |
| `tag` | `--tag`（逗号分隔） |
| `status` | `--status`（逗号分隔；`0` 未完成、`2` 已完成） |

### task move

`--from`、`--to`、`--task` 各写同样次数，每次三元组对应一个对象：`fromProjectId`、`toProjectId`、`taskId`。接口还可带 `sortOrder` 指定在目标清单中的位置；CLI 不传该字段。
