# share-skill 跨项目技能共享 — 操作手册

---

## 这技能是干什么的

在日常开发中，你会在不同项目中安装不同的 Skills、插件、MCP 服务。时间一长就容易出现：

- 在项目 A 装了好用的 `find-skills`，项目 B 里也想用，但不想重新搜索安装
- 忘了某个项目里装过什么能力
- 经常好奇"别人的项目里有什么好东西，能不能装到我这里来"

`share-skill` 解决的就是这三个痛点。

**核心思路：** 每个项目把自己的技能"贡献"到全局池子，其他项目可以"挑选"导入。

```
项目A ──push──→ 全局池 ──pull──→ 项目B
```

---

## 前置准备

### 技能安装位置

`share-skill` 是全局技能，已在以下位置安装：

```
~/.claude/skills/share-skill/SKILL.md
```

### 项目注册表

位于 `~/.claude/skill-registry.json`，记录所有已参与共享的项目：

```json
{
  "projects": {
    "first-cc": "C:/Users/用户名/Desktop/ClaudeCode/first-cc"
  }
}
```

> **首次使用建议：** 在经常用的每个项目里说一次"注册当前项目"，把它们都登记上来。

---

## 能力一：跨项目导入技能

### 命令格式

```
/share_skill --项目名 --技能名
/share_skill 项目名 技能名
```

### 操作流程

#### 情况 A：一切都已就绪

```
你：/share_skill --first-cc --find-skills

Claude：找到技能【find-skills】来自项目【first-cc】
        路径: C:/Users/.../first-cc
        描述: 搜索和发现社区技能
        
        是否导入到当前项目？
```

回复"是"后，技能被复制到当前项目的 `.claude/skills/find-skills/`。

然后 Claude 会问：

> 技能【find-skills】已导入到当前项目。是否永久保留在此项目中？

- **"是"** — 永久保留（默认推荐）
- **"否，仅本次会话使用"** — 保留但提醒可随时删除

#### 情况 B：项目未注册

```
你：/share_skill --my-app --my-skill

Claude：项目【my-app】尚未注册。
        请提供该项目的完整路径，例如 C:/Users/xxx/my-project
```

你提供路径后，Claude 验证该路径下存在 `.claude/skills/` 目录，自动注册并继续导入。

#### 情况 C：技能名记错了

```
你：/share_skill --first-cc --not-exist

Claude：项目【first-cc】中没有名为【not-exist】的技能。
        该项目有以下技能：
        - find-skills        — 搜索和发现社区技能
        - skill-creator      — 创建、评估和改进技能
        - karpathy-guidelines — 卡帕西编程准则
        请选择要导入的技能名称。
```

#### 情况 D：目标已存在同名技能

```
Claude：当前项目中已存在同名技能【find-skills】。是否覆盖？
        - "是" — 覆盖为新版本
        - "否，换个名称" — 输入新名称后导入
        - "取消" — 停止操作
```

### 实操示例

```
# 基础用法：从 first-cc 导入 find-skills
/share_skill --first-cc --find-skills

# 简化写法（不需要 -- 前缀也可以）
/share_skill first-cc find-skills

# 导入后立即使用
"搜索一下有没有公众号排版的skill"
```

---

## 能力二：查看当前项目可用能力

### 命令格式

```
/share_skill nowcanuse
```

### 输出示例

```
📊 当前项目可用能力总览

项目：C:/Users/用户名/Desktop/ClaudeCode/my-app

═══ 🧠 Skills（项目级） ═══
  find-skills       — 搜索和发现社区技能
  karpathy-guidelines — 卡帕西编程准则

═══ 🌐 Skills（全局） ═══
  share-skill       — 跨项目技能共享
  yuque-personal:smart-search — 自然语言搜索语雀知识库
  yuque-personal:reading-digest — 文章阅读摘要
  ...（共 8 个语雀技能）

═══ 🔌 MCP 服务 ═══
  yuque-mcp  — 语雀知识库读写 (npx yuque-mcp)

═══ 📦 插件 ═══
  yuque-personal@yuque — 语雀个人版，16工具+8技能

═══ ⚡ CLI / 命令 ═══
  npx skills find  — 技能搜索
  npx skills add   — 技能安装
  /share_skill     — 跨项目技能共享
```

### 管理菜单

展示完毕后，Claude 会问：

> 你想对这些能力做什么操作？

| 操作 | 说明 | 示例对话 |
|------|------|----------|
| **添加 / install** | 安装新的 skill/MCP/插件 | "加一个 code-review 技能" |
| **删除 / remove** | 移除某个能力 | "删掉 karpathy-guidelines" |
| **更新 / update** | 更新某个能力到最新版 | "更新语雀插件" |
| **注册项目** | 把当前项目加入共享注册表 | "注册当前项目" |
| **查看项目** | 列出所有已注册的共享项目 | "查看已注册的项目" |
| **完成** | 不做任何操作 | "完成" |

---

## 能力三：发现缺失的能力

### 命令格式

```
/share_skill missing
/share_skill available
```

也可以用自然语言：
- "还有什么可以装的"
- "看看别的项目有什么技能"
- "缺失的技能"

### 原理

```
全局 Skills ─────┐
已注册项目的 Skills ├──→ 能力池 ──→ 对比当前项目 ──→ 展示缺失项
全局 Plugins ─────┤
全局 MCP ────────┘
```

**注意：** 全局 Skills 已自动对所有项目生效，不会出现在"缺失"列表中。只有以下内容会展示：
1. **其他已注册项目中的技能** — 不同步不会自动可用
2. **全局已安装但未在当前项目启用的插件**
3. **全局配置了但当前项目没有的 MCP 服务**

### 输出示例

```
🔍 发现可获取但当前项目未使用的能力

═══ 🧠 其他项目的 Skills（可导入） ═══
  来源: first-cc (C:/Users/.../first-cc)
    📥 find-skills          — 搜索、发现和安装社区技能
    📥 skill-creator        — 创建、评估、改进和打包技能
    📥 karpathy-guidelines  — 减少LLM常见编码错误的行为准则

  来源: my-blog (C:/Users/.../my-blog)
    📥 wechat-formatter     — 公众号文章排版
    📥 article-publisher    — 一键发布文章

═══ 📦 未启用的插件 ═══
    ⬜ content-creator      — 跨平台内容创作子代理

═══ 🔌 可添加的 MCP 服务 ═══
  来源: 全局配置 (~/.claude/settings.json)
    ⬜ github-mcp           — GitHub API 集成
```

### 选择方式

Claude 展示完毕后会问：

> 发现以上能力可供添加。你想怎么做？

**单个选择：**
```
"导入 first-cc 的 find-skills"
"开启 content-creator 插件"
"添加 github-mcp"
```

**批量操作：**
```
"全部导入"       → 把所有缺失的 Skills 都导入
"全部开启"       → 把所有未启用的插件都开启
"全选"           → 所有东西都加上
```

**放弃：**
```
"跳过" / "完成"  → 不操作
```

### 逐个确认

选择后，Claude 会逐一确认：

添加 Skill 时：
> 【find-skills】来自项目【first-cc】— 搜索和发现社区技能。是否导入到当前项目？

启用插件时：
> 插件【content-creator】已在全局安装但当前项目未启用。是否启用？

添加 MCP 时：
> MCP【github-mcp】已在全局配置。是否添加到当前项目？

### 完成总结

```
✅ 已完成。本次添加了：
- Skills: find-skills, skill-creator
- 插件: content-creator
- MCP: github-mcp
它们已就绪，你现在可以使用了。
```

---

## 能力四：快速缺失发现（推荐日常使用）

### 命令格式

```
/share_skill --missing-f
/share_skill -missing-f
/share_skill missing-f
```

### 和能力三有什么区别？

功能完全相同，但执行效率大幅提升：

| 对比维度 | `/share_skill missing` | `/share_skill --missing-f` |
|---------|----------------------|--------------------------|
| 工具调用次数 | ~11 次 | **~5-6 次** |
| 文件扫描命中 | 50+（大量噪音） | **~5 个（精确命中）** |
| Token 消耗 | ~8000-10000 | **~2000-3000（省 ~70%）** |
| 插件扫描方式 | 递归通配，扫到大量市场目录文件 | 单层通配，排除 marketplaces/cache |
| 技能描述读取 | 全量读取所有 SKILL.md | 先比目录名，同名跳过不读文件 |

**简单说：结果一样，但快得多、省得多。日常都用这个就行。**

### 原理

少做三件事：
1. **不读同名技能** — 注册项目的技能如果和全局技能同名，说明已经自动可用，跳过不读
2. **不扫市场目录** — 插件扫描只命中真正安装的，不碰 marketplace 和 cache
3. **不分轮** — 所有数据收集在第一轮一次性并行完成，后续全靠内存对比

```
第一轮（全并行）
  ├── 读注册表
  ├── 读全局 settings（插件+MCP）
  ├── 读项目 settings（插件+MCP）
  ├── 列全局技能目录名
  ├── 列项目技能目录名
  ├── 列全局插件目录名（精准过滤）
  └── 列注册项目技能目录名
        ↓
第二轮（纯内存，零IO）
  └── 对比 → 只读真正缺失的 → 展示
```

### 输出示例

```
🔍 缺失能力

═══ 📦 未启用的插件 ═══
    ⬜ content-creator  — 内容创作子代理
```

### 实操

```
# 快速检查有什么能装的
你：/share_skill --missing-f

Claude：发现 1 项缺失：content-creator 插件未启用。

你：开启 content-creator

Claude：✅ 已启用。
```

---

## 完整工作流演示

### 场景：新开了一个项目，想快速配齐常用能力

```
# Step 1：看看当前有什么（大概率空的）
你：/share_skill nowcanuse
Claude：项目只有 2 个技能...

# Step 2：看看其他地方有什么能用的
你：/share_skill missing
Claude：发现 first-cc 里有 3 个技能、全局有 1 个未启用插件、1 个 MCP...

# Step 3：想要的都加上
你：全选
Claude：
  导入 find-skills? → 是 → 永久保留? → 是
  导入 skill-creator? → 是 → 永久保留? → 是
  导入 karpathy-guidelines? → 是 → 永久保留? → 是
  启用 content-creator? → 是
  添加 github-mcp? → 先跳过
  
  ✅ 完成。新增 3 个技能 + 1 个插件。

# Step 4：验证
你：/share_skill nowcanuse
Claude：现在有 5 个技能、2 个插件、1 个 MCP... ✅
```

### 场景：只想从别人的项目里借一个技能

```
你：/share_skill --my-blog --wechat-formatter
Claude：找到【wechat-formatter】，是否导入? → 是
        ✅ 已导入，是否永久保留? → 是
```

---

## 项目注册表管理

### 查看已注册项目

在 `/share_skill nowcanuse` 后选择"查看项目"。

### 注册当前项目

在 `/share_skill nowcanuse` 后选择"注册项目"，Claude 会询问：
> 输入项目名和路径

也可以自然语言说：
> "注册当前项目，叫 my-app"

### 手动编辑注册表

文件位置：`~/.claude/skill-registry.json`

```json
{
  "projects": {
    "first-cc": "C:/Users/用户名/Desktop/ClaudeCode/first-cc",
    "my-app": "C:/Users/用户名/Desktop/my-app",
    "blog-project": "D:/projects/blog"
  }
}
```

---

## 技能发现机制（让共享生效的关键）

Claude Code 的技能发现优先级：

```
1. 全局 ~/.claude/skills/        ← share-skill 在这，所有项目自动可用
2. 项目 .claude/skills/          ← 通过 share-skill 导入的技能在这
```

所以你把 `.claude/skills/` 里的技能同步到全局 `~/.claude/skills/` 后，所有项目立即可用，**不需要在每个项目里单独导入**。

只有当你希望项目独立管理技能（不依赖全局）时，才用 `/share_skill --项目 --技能` 做项目级导入。

---

## 常见问题

### Q：全局技能和项目级技能有什么区别？

| | 全局技能 | 项目级技能 |
|------|----------|----------|
| 位置 | `~/.claude/skills/` | `项目/.claude/skills/` |
| 作用范围 | 所有项目自动可用 | 仅当前项目 |
| 导入方式 | `share-skills.sh --push` | `/share_skill --项目 --技能` |
| 适用场景 | 通用技能（karpathy-guidelines） | 项目特定技能 |

### Q：我怎么知道一个项目里有什么技能？

```
/share_skill --项目名 --任意不存在的技能名
```

Claude 会列出该项目的全部可用技能。

### Q：导入后技能更新了怎么办？

目前需要重新导入覆盖。可以先删除旧技能再重新导入，或在 `/share_skill nowcanuse` 管理菜单中选择"更新"。

### Q：能不能把技能同步回全局？

用项目里的 `share-skills.sh` 脚本：

```bash
./share-skills.sh --push
```

### Q：技能名称冲突怎么办？

Claude 会询问你是否覆盖，选择"换个名称"可避免冲突。
