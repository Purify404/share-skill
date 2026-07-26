# 技能链接操作手册

> skill-index v3 四功能（增删改查）的详细执行步骤。
> 从 SKILL.md 的触发表跳转至此。

## 路径常量（本文共用）

```
SKILL_LIBRARY = <SKILL_LIBRARY>
YEAR_SKILLS   = <PROJECT>/.claude/skills
GLOBAL_SKILLS = ~/.claude/skills
```

## 命令模板

**检测是否为 Junction（Python）**：
```python
import subprocess
def is_junction(path):
    r = subprocess.run(['powershell', '-Command',
        f'(Get-Item -Path "{path}").Attributes -match "ReparsePoint"'],
        capture_output=True, text=True)
    return 'True' in r.stdout

def get_target(path):
    r = subprocess.run(['powershell', '-Command',
        f'(Get-Item -Path "{path}").Target'],
        capture_output=True, text=True)
    return r.stdout.strip()
```

**创建 Junction**（PowerShell）：
```powershell
New-Item -Path "<YEAR_SKILLS>/<名称>" -ItemType Junction -Target "<源绝对路径>"
```

**删除 Junction**：
```powershell
cmd /c "rmdir <YEAR_SKILLS>/<名称>"
```

---

## 一、增 — 创建 Symlink

### 触发词
"把 XXX 加入年项目" / "链接 XXX" / "安装 XXX 技能" / "启用 XXX"

### 前置

已加载 `references/locations.md`（可链接技能清单）。

### 流程

1. **查找源路径**：在 locations.md 中搜索技能名，获取源绝对路径。找不到 → 提示"XXX 不在可链接清单中，是否需要更新技能索引？"
2. **检查是否已链接**：用 Python `is_junction(f"{YEAR_SKILLS}\\{name}")` 检测是否已存在 Junction。已存在 → 告知 Noel"XXX 已链接"，停止
3. **检查目标路径存在**：`os.path.exists(源绝对路径)` → 不存在则报错
4. **创建 Junction**：执行 PowerShell `New-Item` 命令
5. **验证**：检查 Junction 创建成功 + 目标可访问
6. **更新 allsymlink.md**：执行"刷新 allsymlink.md"步骤（见 §查）

### 输出

```
✅ 已将 XXX 加入年项目
   Symlink: <YEAR_SKILLS>/XXX → <源路径>
```

---

## 二、删 — 删除 Symlink

### 触发词
"把 XXX 从年项目移除" / "卸载 XXX" / "移除 XXX 技能"

### 流程

1. **确认是 Junction**：`is_junction(f"{YEAR_SKILLS}\\{name}")` → 不是 Junction → 拒绝操作（实体目录不能通过 skill-index 删除）
2. **征求确认**：告知 Noel"将从年项目移除 XXX 的 symlink（源文件不受影响），是否继续？"
3. **等 Noel 确认后**，执行：`cmd /c "rmdir <YEAR_SKILLS>/<名称>"`
4. **验证**：确认路径已不存在
5. **更新 allsymlink.md**：执行"刷新 allsymlink.md"步骤

### 安全规则

- **绝不删除实体目录**，只删除 Junction
- **绝不删除源文件**（rmdir 只删除链接本身）
- 必须 Noel 确认

### 输出

```
🗑️ 已从年项目移除 XXX（源文件未受影响）
```

---

## 三、改 — 维护来源项目 + 同步技能清单

### 3a. 管理 projects.md（来源项目注册表）

#### 查 — "有哪些技能来源？" / "显示 projects"

读取 `references/projects.md` → 输出项目列表表格。

#### 增 — "添加技能来源"

1. 询问 Noel：项目名、路径、类型（global/year/library/library-book）
2. 追加一行到 projects.md 的表格中
3. 输出新增结果

#### 删 — "删除技能来源 <项目名>"

1. 确认后从表格删除对应行
2. 提醒：下次"更新技能索引"时，来自该项目的技能条目将从 locations.md 移除

#### 改 — "修改技能来源 <项目名>"

1. 询问 Noel：修改哪个字段（路径/说明）
2. 更新对应行

### 3b. 同步 locations.md（"更新技能索引" / "刷新技能注册表" / "同步 locations"）

#### 触发词
"更新技能索引" / "刷新技能注册表" / "同步 locations.md" / "扫描新技能"

#### 流程

1. **读 projects.md** → 获取所有 `library` 和 `library-book` 类型项目的路径
2. **扫描每个来源项目**：对每个路径，用 Python `os.listdir()` 列出子目录，检查是否含 `SKILL.md`
3. **检查全局安装**：对扫描到的技能，检查 `~/.claude/skills/<name>` 是否存在 → 存在则标记为全局可用（无需链接）
4. **对比 locations.md**：
   - 新技能（目录存在但不在 locations.md 中）→ 准备新增
   - 失效技能（在 locations.md 中但目录不存在且非全局）→ 准备删除
   - 路径变化 → 准备更新
5. **列出变更摘要**，征求 Noel 确认：
   ```
   📋 技能索引变更：
   新增 2：browser-act, browser-act-skill-forge（已在全局，标注）
   失效 0
   路径变更 0
   是否更新 locations.md？
   ```
6. **确认后写入**：用 Edit 工具更新 locations.md，保持路径常量 + 分类表格格式，新增条目按表格模板追加
7. **告知结果**：`locations.md 已更新（新增 X，删除 Y，更新 Z）`

---

## 四、查 — Symlink 状态

### 4a. 查看全部状态（"有哪些技能可用？" / "显示链接状态"）

1. **读 locations.md** → 获取全部可链接技能
2. **扫描年项目** → 用 Python 检查每个技能在 `YEAR_SKILLS` 下是否存在 Junction
3. **扫描全局** → 检查 `GLOBAL_SKILLS` 下是否存在
4. **读 allsymlink.md** → 获取已记录的 symlink 列表
5. **输出表格**：

```markdown
| 技能名 | 来源 | 状态 |
|--------|------|------|
| skill-index | skill库-skills | ✅ 已链接 |
| math-solving-standard | skill库-skills | ❌ 未链接 |
| lesson-plan-prep | skill库-.claude | ❌ 未链接 |
| pptx | skill库-.claude | ❌ 未链接 |
| pandoc | 全局 | 🌐 全局可用（无需链接） |
| browser-act | 全局 | 🌐 全局可用（无需链接） |
| advanced-algebra | skill库-books | 📁 实体目录 |
| ... | ... | ... |
```

状态列说明：
- ✅ 已链接 = Junction 存在
- ❌ 未链接 = locations.md 有记录，未链接，可安装
- 🌐 全局可用 = 在 `~/.claude/skills/`，无需操作
- 📁 实体目录 = 存在但不是 Junction（不能通过 skill-index 管理）

6. **底部统计**：
```
已链接: X | 未链接: Y | 全局可用: Z | 实体目录: W
```

### 4b. 查看单个技能（"XXX 链接了吗？" / "XXX 的状态"）

1. 检查 `YEAR_SKILLS/XXX` 是否存在及其类型
2. 检查 `GLOBAL_SKILLS/XXX` 是否存在
3. 输出单行状态

### 4c. 刷新 allsymlink.md（内部操作，增删后自动执行）

用 Python 扫描年项目所有 Junction → 生成表格 → 覆写 allsymlink.md 的表格区域。

Python 扫描命令：
```python
import os
import subprocess

year_skills = r'<PROJECT>/.claude/skills'
for name in sorted(os.listdir(year_skills)):
    path = os.path.join(year_skills, name)
    r = subprocess.run(['powershell', '-Command',
        f'(Get-Item -Path "{path}").Attributes -match "ReparsePoint"'],
        capture_output=True, text=True)
    if 'True' in r.stdout:
        r2 = subprocess.run(['powershell', '-Command',
            f'(Get-Item -Path "{path}").Target'],
            capture_output=True, text=True)
        target = r2.stdout.strip()
        # 判断来源项目
        if 'books' in target:
            source = 'skill库-books'
        elif 'skills' in target and 'skill-index' in target:
            source = 'skill库-skills'
        elif '.claude' in target and 'skills' in target:
            source = 'skill库-.claude'
        else:
            source = '未知'
        print(f'| {name} | {target} | {source} |')
```

然后更新 allsymlink.md 中的表格和最后更新时间。
