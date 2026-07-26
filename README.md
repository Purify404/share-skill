# skill-index — Claude Code 技能链接管理器

管理 Claude Code 技能库与项目之间的 symlink。四功能：**增删改查**。

## 功能

| 操作 | 说明 |
|------|------|
| **增** | 将技能通过 symlink 安装到项目中 |
| **删** | 从项目中移除技能链接 |
| **改** | 同步 skills 索引，管理技能来源注册表 |
| **查** | 查看所有可用技能、链接状态 |

## 使用方法

将此仓库克隆为 Claude Code 技能：

```bash
git clone https://github.com/Purify404/share-skill.git ~/.claude/skills/skill-index
```

## 结构

```
SKILL.md              # 技能主文档
references/
├── operations.md     # 操作手册（增删改查详细步骤）
├── projects.md       # 技能来源项目注册表
├── locations.md      # 可链接技能清单
└── allsymlink.md     # 当前 symlink 快照
```

## 许可

MIT
