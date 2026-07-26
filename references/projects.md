# 技能来源项目注册表

> 记录所有技能来源的"项目"（目录）。`locations.md` 的技能清单通过扫描此处列出的项目生成。
> 支持增删改查。

## 路径常量

| 常量 | 绝对路径 |
|------|----------|
| `GLOBAL_SKILLS` | `~/.claude/skills` |
| `SKILL_LIBRARY` | `<SKILL_LIBRARY>` |
| `YEAR_SKILLS` | `<PROJECT>/.claude/skills` |

## 项目列表

| 项目名 | 路径 | 类型 | 说明 |
|--------|------|------|------|
| 全局技能 | `GLOBAL_SKILLS` | `global` | 所有项目自动可用，无需 symlink |
| 年项目 | `YEAR_SKILLS` | `year` | 年项目本地技能（symlink 目标位置） |
| skill库-skills | `SKILL_LIBRARY/skills` | `library` | 自建技能源文件 |
| skill库-.claude | `SKILL_LIBRARY\.claude/skills` | `library` | 项目本地技能（社区+自建） |
| skill库-books | `SKILL_LIBRARY/books` | `library-book` | 书籍技能（数学教材） |

## 类型说明

| 类型 | 含义 | symlink 策略 |
|------|------|-------------|
| `global` | 全局安装，所有项目自动可用 | 无需链接 |
| `year` | 年项目本地，symlink 的目标位置 | 本身即目标，不需从它链接 |
| `library` | skill 库中的技能源 | 可链接到年项目 |
| `library-book` | 书籍技能，通常预链接 | 默认已链接，一般不动 |

## 增删改查操作

### 查（列出所有来源项目）

"有哪些技能来源？" / "显示 projects" → 输出上表。

### 增（添加新来源项目）

"添加技能来源" / "新增来源项目" → 询问：项目名、路径、类型 → 追加到表格。

### 删（删除来源项目）

"删除技能来源 <项目名>" → 确认后删除对应行。**注意**：删除来源后，`locations.md` 中来自该项目的技能条目将在下次"更新技能索引"时被移除。

### 改（修改来源项目）

"修改技能来源 <项目名>" → 询问：新路径或新说明 → 更新对应行。
