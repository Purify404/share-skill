# 可链接 Skill 源路径

> 每项技能的源绝对路径。由 `projects.md` 中列出的来源项目扫描生成。
> 维护方式：执行"更新技能索引"（见 `operations.md#三`）。

## 路径常量

| 常量 | 绝对路径 |
|------|----------|
| `SKILL_LIBRARY` | `<SKILL_LIBRARY>` |
| `YEAR_SKILLS` | `<PROJECT>/.claude/skills` |
| `GLOBAL_SKILLS` | `~/.claude/skills` |

### Symlink 命令

**创建链接**（PowerShell）：
```
New-Item -Path "<YEAR_SKILLS>/<名称>" -ItemType Junction -Target "<源绝对路径>"
```

**删除链接**：
```
cmd /c "rmdir <YEAR_SKILLS>/<名称>"
```

### 已全局安装无需链接的技能

以下技能已安装在 `~/.claude/skills/`，所有项目自动可用：agent-reach、pandoc、read-arxiv-paper、browser-act、browser-act-skill-forge、skill-creator、find-skills、share-skill、dispatch、sandbox-agent、reflect、karpathy-guidelines、xiaonian-memory、book-to-skill、book-skill-manager。

---

## 自建技能（skills/）

> 来源项目：`skill库-skills`

| 名称 | 源绝对路径 | 说明 |
|------|-----------|------|
| skill-index | `<SKILL_LIBRARY>/skills/skill-index\` | 技能链接管理器（自身） |
| math-solving-standard | `<SKILL_LIBRARY>/skills/math-solving-standard\` | 数学解题规范（五模块+审查） |
| obsidian-vault-notes | `<SKILL_LIBRARY>/skills/obsidian-vault-notes\` | Claudian 专用 Vault 检索-编辑 |
| obsidian-plugin | `<SKILL_LIBRARY>/skills/obsidian-plugin\` | Obsidian 插件开发（社区技能，gapmiss） |

## 自建技能（.claude/skills/）

> 来源项目：`skill库-.claude`

| 名称 | 源绝对路径 | 说明 |
|------|-----------|------|
| lesson-plan-prep | `<SKILL_LIBRARY>/.claude/skills/lesson-plan-prep\` | 教案备课全流程 |
| paper-research | `<SKILL_LIBRARY>/.claude/skills/paper-research\` | 论文调研全流程 |
| daily-planner | `<SKILL_LIBRARY>/.claude/skills/daily-planner\` | 每日/周/月规划 |
| oral-history | `<SKILL_LIBRARY>/.claude/skills/oral-history\` | 口述史·碎片记录 |

## 社区技能（.claude/skills/，仅项目本地）

> 来源项目：`skill库-.claude`

| 名称 | 源绝对路径 | 说明 |
|------|-----------|------|
| pptx | `<SKILL_LIBRARY>/.claude/skills/pptx\` | 创建/编辑 PPT |
| docx | `<SKILL_LIBRARY>/.claude/skills/docx\` | 创建/编辑 Word |
| xlsx | `<SKILL_LIBRARY>/.claude/skills/xlsx\` | 创建/编辑 Excel |
| pdf | `<SKILL_LIBRARY>/.claude/skills/pdf\` | PDF 读取/提取/填表 |
| humanizer | `<SKILL_LIBRARY>/.claude/skills/humanizer\` | AI 文字去味 🎤 |
| best-minds | `<SKILL_LIBRARY>/.claude/skills/best-minds\` | 顶级专家模拟 🎤 |

## 书籍技能（books/）

> 来源项目：`skill库-books`

| 名称 | 源绝对路径 | 说明 |
|------|-----------|------|
| math-analysis-vol1 | `<SKILL_LIBRARY>/books/math-analysis-vol1\` | 数学分析上册（第1-11章） |
| math-analysis-vol2 | `<SKILL_LIBRARY>/books/math-analysis-vol2\` | 数学分析下册（第12-23章） |
| math-analysis-exercises | `<SKILL_LIBRARY>/books/math-analysis-exercises\` | 数学分析课后习题解答 |
| math-analysis-continuation | `<SKILL_LIBRARY>/books/math-analysis-continuation\` | 分析续论课程讲义（10讲） |
| advanced-algebra | `<SKILL_LIBRARY>/books/advanced-algebra\` | 高等代数第五版 |
| advanced-algebra-exercises | `<SKILL_LIBRARY>/books/advanced-algebra-exercises\` | 高等代数习题解答 |
