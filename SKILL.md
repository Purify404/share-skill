---
name: skill-index
description: |
  技能链接管理器。管理「年」项目与 skill 库之间的 symlink——按需将技能"安装"到年项目或从年项目移除。
  四个功能：增（创建 symlink）、删（删除 symlink）、改（维护 projects.md + locations.md）、查（显示 symlink 状态）。
  触发词："有哪些技能可用""把 XXX 加入年项目""把 XXX 从年项目移除""更新技能索引""添加技能来源""XXX 链接了吗"。
---

# 技能链接管理器

管理 skill 库 → 年项目的 symlink。四功能：**增删改查**。

---

## 触发机制

| 用户说 | 功能 | 详情 |
|--------|------|------|
| "把 XXX 加入年项目" / "链接 XXX" / "安装 XXX" | **增**：创建 symlink | `references/operations.md#一` |
| "把 XXX 从年项目移除" / "卸载 XXX" | **删**：删除 symlink | `references/operations.md#二` |
| "更新技能索引" / "刷新技能注册表" | **改**：同步 locations.md | `references/operations.md#三b` |
| "添加技能来源" / "删除技能来源" / "有哪些技能来源" | **改**：管理 projects.md | `references/operations.md#三a` |
| "有哪些技能可用？" / "显示链接状态" | **查**：全部状态 | `references/operations.md#四a` |
| "XXX 链接了吗？" / "XXX 的状态" | **查**：单个查询 | `references/operations.md#四b` |

---

## 核心概念

| 概念 | 含义 |
|------|------|
| **已链接** ✅ | 年项目存在有效 Junction → skill 可用 |
| **未链接** ❌ | skill 在 locations.md 中，但无 Junction → 可安装 |
| **全局可用** 🌐 | 在 `~/.claude/skills/` → 无需链接，自动可用 |
| **实体目录** 📁 | 年项目中存在但不是 Junction → 不受 skill-index 管理 |

---

## 路径常量

| 常量 | 绝对路径 |
|------|----------|
| `SKILL_LIBRARY` | `<SKILL_LIBRARY>` |
| `YEAR_SKILLS` | `<PROJECT>/.claude/skills` |
| `GLOBAL_SKILLS` | `~/.claude/skills` |

---

## L2 文件索引

| 文件 | 加载时机 | 内容 |
|------|----------|------|
| `references/operations.md` | 执行任何 CRUD 操作 | 增删改查详细步骤、命令模板、Python 检测脚本 |
| `references/projects.md` | "改"操作 / 维护技能来源 | 技能来源项目注册表（增删改查项目） |
| `references/locations.md` | "增""查"操作 | 可链接技能清单（名称+源路径+说明） |
| `references/allsymlink.md` | "查"操作 / 增删后自动更新 | 当前 symlink 快照 |

### 数据流

```
projects.md           locations.md          allsymlink.md
(技能从哪来)    →     (有哪些技能)    →     (哪些已链接)
   改                     改                    增/删
```

- **projects.md** → 记录来源项目（全局、年项目、skill库各子目录）
- **locations.md** → 从 projects 扫描得到的可链接技能清单
- **allsymlink.md** → 年项目当前 symlink 快照（增删后自动刷新）
