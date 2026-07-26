# 当前 Symlink 快照

> 自动维护。每次增/删 symlink 后立即更新。
> 最后更新：2026-07-24

## 年项目已链接技能（symlink）

| 技能名 | 目标路径 | 来源项目 |
|--------|----------|----------|
| baoyu-article-illustrator | `~/.claude/skills/baoyu-article-illustrator\` | global |
| baoyu-cover-image | `~/.claude/skills/baoyu-cover-image\` | global |
| baoyu-format-markdown | `~/.claude/skills/baoyu-format-markdown\` | global |
| baoyu-markdown-to-html | `~/.claude/skills/baoyu-markdown-to-html\` | global |
| baoyu-post-to-wechat | `~/.claude/skills/baoyu-post-to-wechat\` | global |
| book-to-cards | `SKILL_LIBRARY/skills/book-to-cards\` | skill库-skills |
| book-to-skill | `~/.claude/skills/book-to-skill\` | global |
| daily-planner | `SKILL_LIBRARY/.claude/skills/daily-planner\` | skill库-.claude |
| math-solving-standard | `SKILL_LIBRARY/skills/math-solving-standard\` | skill库-skills |
| obsidian-plugin | `SKILL_LIBRARY/skills/obsidian-plugin\` | skill库-skills |
| oral-history | `SKILL_LIBRARY/.claude/skills/oral-history\` | skill库-.claude |
| reflect | `~/.claude/skills/reflect\` | global |
| skill-index | `SKILL_LIBRARY/skills/skill-index\` | skill库-skills |

<!-- 以上表格由 skill-index 的增/删操作自动维护 -->

## 年项目中的实体目录（非 symlink，不受 skill-index 管理）

以下技能以实体目录形式存在于年项目中，非 symlink：

| 技能名 | 说明 |
|--------|------|
| advanced-algebra | 书籍技能（实体目录） |
| advanced-algebra-exercises | 书籍技能（实体目录） |
| math-analysis-continuation | 书籍技能（实体目录） |
| math-analysis-exercises | 书籍技能（实体目录） |
| math-analysis-vol1 | 书籍技能（实体目录） |
| math-analysis-vol2 | 书籍技能（实体目录） |

> 注：书籍技能通常通过 `book-to-skill` 直接生成到年项目，不走 symlink 流程。如需改为 symlink 管理，先删除实体目录再用 skill-index 创建链接。

## 全局可用技能（无需链接）

以下技能安装在 `~/.claude/skills/`，所有项目自动可用：

agent-reach · book-to-skill · browser-act · browser-act-skill-forge · dispatch · find-skills · karpathy-guidelines · pandoc · read-arxiv-paper · reflect · sandbox-agent · share-skill · skill-creator

## 更新方法

执行 `references/operations.md#查` 中的"刷新 allsymlink.md"步骤。
