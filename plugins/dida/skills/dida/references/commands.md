# dida CLI 完整命令参考

`dida` 的全部命令、参数与示例。本文件是 SKILL.md 的展开参考——只有当你需要查某个命令的完整选项、或确认字段写法时才读它，不必整篇加载。

> 命令行里所有 `<projectId>` / `<taskId>` / `<habitId>` 等 ID，都是 ObjectId 十六进制串，必须先用 `project list --json` / `project data --json` / `habit list --json` 反查得到（见 SKILL.md「三条命脉」）。

## 认证（auth）

```bash
dida auth login            # OAuth 浏览器登录（推荐，PKCE）
dida auth token <token>    # 直接写入 access token（无浏览器环境）
dida auth status           # 查看是否已登录
dida auth logout           # 清除本地 token
```

Token 获取：网页版滴答清单 → 头像 → 设置 → 账户与安全 → API 口令。

## 任务（task）

```bash
# 获取
dida task get <projectId> <taskId>

# 创建
dida task create --title "买牛奶" --project <projectId>
dida task create --title "开会" --project <projectId> --priority 5 --due-date "2026-03-10T09:00:00+0000"
dida task create --title "复盘" --project <projectId> --tags 工作,紧急

# 更新（⚠️ 三件套：<taskId> + --id <taskId> + --project <projectId>）
dida task update <taskId> --id <taskId> --project <projectId> --title "新标题"
dida task update <taskId> --id <taskId> --project <projectId> --tags 工作,紧急
dida task update <taskId> --id <taskId> --project <projectId> --parent-id <parentTaskId>
dida task update <taskId> --id <taskId> --project <projectId> --parent-id null   # 取消父子关联
dida task update <taskId> --id <taskId> --project <projectId> --estimated-duration 1500 --estimated-pomo 5

# 完成 / 删除
dida task complete <projectId> <taskId>
dida task delete <projectId> <taskId>

# 在清单间移动任务
dida task move --from <sourceProjectId> --to <destProjectId> --task <taskId>

# 列出已完成任务
dida task completed --projects <projectId> --start-date "2026-03-01T00:00:00+0000" --end-date "2026-03-09T23:59:59+0000"

# 过滤任务
dida task filter --projects <projectId> --priority 3,5 --status 0

# 任务评论
dida task comment list <projectId> <taskId>
dida task comment add <projectId> <taskId> --title "已处理"
dida task comment delete <projectId> <taskId> <commentId>
```

### task create / update 常用选项

| 选项 | 含义 |
|------|------|
| `--title` | 标题，宜短；长文用 `--content` |
| `--content` | 普通/笔记类任务的正文 |
| `--desc` | 清单类任务的说明 |
| `--project` | 所属清单（projectId），**必填** |
| `--priority` | `0`无 `1`低 `3`中 `5`高 |
| `--start-date` / `--due-date` | ISO 8601 带时区；两者不同表示一段区间 |
| `--all-day` | 是否全天 |
| `--time-zone` | 时区 |
| `--tags` | 标签，逗号分隔 |
| `--repeat` | 重复规则 `RRULE:...` / `ERULE:...`（见 fields.md） |
| `--reminders` | 提醒触发串（见 fields.md） |
| `--items` | 检查事项 |
| `--parent-id` | 父任务 id；`null`/`none` 取消关联 |
| `--sort-order` | 排序 |
| `--estimated-duration` | 预计专注时长（秒） |
| `--estimated-pomo` | 预计番茄数（最大 60） |

## 清单（project）

```bash
dida project list                    # 列出清单
dida project get <projectId>         # 清单详情
dida project data <projectId>        # 清单 + 任务 + 分组（找 taskId 用这个）
dida project create --name "工作" --color "#F18181" --view-mode list --kind TASK
dida project update <projectId> --name "新名字" --color "#4AB8A9"
dida project delete <projectId>
```

### 清单分组（文件夹）

```bash
dida project group list
dida project group create --name "工作"
dida project group update <groupId> --name "个人"
dida project group delete <groupId>
```

### 看板列（column）

```bash
dida project column list <projectId>
dida project column create <projectId> --name "进行中"
dida project column update <projectId> <columnId> --name "已完成"
```

### project create / update 选项

| 选项 | 含义 |
|------|------|
| `--name` | 名称 |
| `--color` | 颜色，如 `#F18181` |
| `--view-mode` | `list` / `kanban` / `timeline` |
| `--kind` | `TASK` 或 `NOTE` |

## 标签（tag）

```bash
dida tag list
dida tag create --name urgent --label urgent
```

## 习惯（habit）

```bash
dida habit get <habitId>
dida habit list
dida habit create --name "每天喝水" --repeat "RRULE:FREQ=DAILY;INTERVAL=1" --goal 8 --unit 杯
dida habit update <habitId> --name "每天喝水 2L" --goal 2000 --unit ml

# 打卡（stamp 为 YYYYMMDD）
dida habit checkin <habitId> --stamp 20260424 --value 1 --goal 1

# 按日期范围查询打卡（from/to 为 YYYYMMDD）
dida habit checkins --habits <habitId> --from 20260401 --to 20260430
```

## 专注（focus）

```bash
dida focus get <focusId> --type pomodoro

# 列出时间范围内的专注记录（⚠️ 最大 30 天）
dida focus list --from "2026-04-01T00:00:00+0800" --to "2026-04-07T23:59:59+0800" --type pomodoro

# 创建专注记录
dida focus create --type pomodoro --task-id <taskId> \
  --start-time "2026-04-07T09:00:00+0800" --end-time "2026-04-07T09:25:00+0800" --duration 1500

dida focus delete <focusId> --type pomodoro
```

## 倒数日（countdown）

```bash
dida countdown list
```

## JSON 输出

任意命令加 `--json` 输出原始 API JSON（camelCase 字段）：

```bash
dida project list --json
dida task get <projectId> <taskId> --json
```

> 给 agent 自己处理的数据，一律加 `--json`（见 SKILL.md「命脉 2」）。

## API mapping

每个命令对应的 DIDA Open API 接口，方便排查字段差异：

| 命令 | Endpoint |
|------|----------|
| `task get` | `GET /project/{projectId}/task/{taskId}` |
| `task create` | `POST /task` |
| `task update` | `POST /task/{taskId}` |
| `task complete` | `POST /project/{projectId}/task/{taskId}/complete` |
| `task delete` | `DELETE /project/{projectId}/task/{taskId}` |
| `task move` | `POST /task/move` |
| `task completed` | `POST /task/completed` |
| `task filter` | `POST /task/filter` |
| `task comment list` | `GET /project/{projectId}/task/{taskId}/comments` |
| `task comment add` | `POST /project/{projectId}/task/{taskId}/comment` |
| `task comment delete` | `DELETE /project/{projectId}/task/{taskId}/comment/{id}` |
| `project list` | `GET /project` |
| `project get` | `GET /project/{projectId}` |
| `project data` | `GET /project/{projectId}/data` |
| `project create` | `POST /project` |
| `project update` | `POST /project/{projectId}` |
| `project delete` | `DELETE /project/{projectId}` |
| `project group list` | `GET /project/group` |
| `project group create` | `POST /project/group` |
| `project group update` | `POST /project/group/{projectGroupId}` |
| `project group delete` | `DELETE /project/group/{projectGroupId}` |
| `project column list` | `GET /project/{projectId}/column` |
| `project column create` | `POST /project/{projectId}/column` |
| `project column update` | `POST /project/{projectId}/column/{columnId}` |
| `tag list` | `GET /tag` |
| `tag create` | `POST /tag` |
| `habit get` | `GET /habit/{habitId}` |
| `habit list` | `GET /habit` |
| `habit create` | `POST /habit` |
| `habit update` | `POST /habit/{habitId}` |
| `habit checkin` | `POST /habit/{habitId}/checkin` |
| `habit checkins` | `GET /habit/checkins` |
| `focus get` | `GET /focus/{focusId}?type=` |
| `focus list` | `GET /focus?from=&to=&type=` |
| `focus create` | `POST /focus` |
| `focus delete` | `DELETE /focus/{focusId}?type=` |
| `countdown list` | `GET /countdown` |
