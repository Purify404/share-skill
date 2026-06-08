---
name: share-skill
description: Cross-project skill sharing — import skills from other projects, list all available capabilities (skills, MCPs, plugins, CLIs) in the current project, discover missing capabilities available elsewhere. Use when the user says "/share_skill", "share skill", "cross-project skill", "导入其他项目的技能", "跨项目共享技能", "现在能用的技能", "what can I use", "缺失的技能", "还有什么可以装", "missing skills".
---

# Share Skill — 跨项目技能共享与管理

## 概述

三个核心能力：
1. **跨项目导入技能** — 通过项目名 + 技能名从其他项目拉取技能
2. **能力清单** — 查看当前项目所有可用的 skills、MCP、插件、CLI 工具
3. **缺失能力发现** — 对比全局和已注册项目，找出当前项目没有但可获取的能力

## 项目注册表

项目注册表位于 `~/.claude/skill-registry.json`，格式：

```json
{
  "projects": {
    "first-cc": "/path/to/your/skills-project",
    "my-app": "/path/to/another-project"
  }
}
```

在使用任何功能前，先读取该文件确认已有注册。如果注册表不存在，创建空注册表。

---

## 能力一：/share_skill --<项目名> --<技能名>

从指定项目导入一个技能到当前项目。

### 流程

#### Step 1：解析参数

用户输入格式：
- `/share_skill --first-cc --find-skills`
- `/share_skill --项目名 --技能名`
- `/share_skill 项目名 技能名`（简化版）

#### Step 2：查找源项目

读取 `~/.claude/skill-registry.json`，查找 `项目名`。

**如果未找到：**
用中文告知用户该项目未注册，询问项目路径：
> "项目【XXX】尚未注册。请提供该项目的完整路径，例如 C:/Users/xxx/project-folder"

收到路径后：
1. 验证 `.claude/skills/` 目录是否存在
2. 如果存在，将该项目加入注册表
3. 继续下一步

#### Step 3：查找源技能

在源项目的 `.claude/skills/<技能名>/` 下查找 `SKILL.md`。

**如果技能存在：** 用中文告知用户：
> "找到技能【技能名】来自项目【项目名】(路径: xxx)。是否导入到当前项目？"

**如果技能不存在：** 列出源项目所有可用技能，让用户选择：
> "项目【项目名】中没有名为【技能名】的技能。该项目有以下技能：
> - XXX
> - YYY
> 请选择要导入的技能名称。"

#### Step 4：执行导入

用户确认后，将技能目录复制到当前项目的 `.claude/skills/<技能名>/`。

导入完成后，询问用户：
> "技能【技能名】已导入到当前项目。是否永久保留在此项目中？"
> - "是" — 保留（默认）
> - "否，仅本次会话使用" — 保留但提醒用户会话结束后可手动删除

**如果目标位置已存在同名技能：**
> "当前项目中已存在同名技能【技能名】。是否覆盖？"
> - "是" — 覆盖
> - "否，换个名称" — 询问新名称
> - "取消" — 停止

#### Step 5：确认完成

> "✅ 技能【技能名】已就绪，你现在可以使用了。"

---

## 能力二：/share_skill nowcanuse

查看当前项目所有可用能力，并提供管理选项。

### 流程

#### Step 1：全面扫描

扫描以下所有内容：

**1. Skills（项目级 `.claude/skills/`）**
```bash
# 列出所有 SKILL.md 文件，提取 name 和 description
```

**2. Skills（全局 `~/.claude/skills/`）**
```bash
# 同上
```

**3. MCP Servers**
读取 `.claude/settings.local.json` 和 `.claude/settings.json` 中的 `mcpServers` 字段。
同时检查全局 `~/.claude/settings.json`。

**4. 插件 (Plugins)**
读取 `.claude/settings.local.json` 和 `.claude/settings.json` 中的 `enabledPlugins` 字段。
同时检查 `~/.claude/plugins/` 目录下的插件目录。

**5. CLI 工具**
检查以下位置：
- 当前项目的 `package.json` 中的 `bin` 字段
- `.claude/commands/` 目录中的自定义 slash 命令
- 常见全局 CLI（如果存在于 PATH）：`npx skills`

#### Step 2：格式化展示

用中文表格展示所有发现的能力：

```
📊 当前项目可用能力总览

项目：C:/Users/xxx/current-project

═══ 🧠 Skills（项目级） ═══
  find-skills       — 搜索和发现社区技能
  karpathy-guidelines — 卡帕西编程准则

═══ 🌐 Skills（全局） ═══
  yuque-personal:smart-search  — 自然语言搜索语雀知识库
  ...

═══ 🔌 MCP 服务 ═══
  yuque-mcp  — 语雀知识库读写 (npx yuque-mcp)

═══ 📦 插件 ═══
  yuque-personal@yuque — 语雀个人版，16工具+8技能
  security-guidance@... — 安全审查

═══ ⚡ CLI / 命令 ═══
  npx skills find  — 技能搜索
  npx skills add   — 技能安装
```

#### Step 3：管理选项

展示完毕后，用中文询问：

> "你想对这些能力做什么操作？"
> - "添加 / install" — 安装新的 skill/MCP/插件
> - "删除 / remove" — 移除某个能力
> - "更新 / update" — 更新某个能力
> - "注册项目" — 注册新项目到共享注册表
> - "查看项目" — 列出已注册的共享项目
> - "完成" — 不做任何操作

根据用户选择执行对应操作：

**添加：**
- Skill → 用 `npx skills find` 搜索或用 `find-skills` 技能
- MCP → 用 `claude mcp add`
- 插件 → 用 `claude plugin install`

**删除：**
- 列出可删除项，确认后删除
- Skill → 删除 `.claude/skills/xxx/` 目录
- 插件 → 用 `claude plugin uninstall`
- MCP → 用 `claude mcp remove`

**更新：**
- Skill → 从源项目重新复制
- 插件 → 用 `claude plugin update`
- MCP → 重新添加

**注册项目：**
- 询问项目名和路径
- 写入 `~/.claude/skill-registry.json`

**查看项目：**
- 读取 `~/.claude/skill-registry.json` 并展示

---

## 能力三：/share_skill missing

发现当前项目**缺失**但可以从其他地方获取的能力，并提供一键添加。

### 触发方式

- `/share_skill missing`
- `/share_skill available`
- "还有什么可以装的"
- "看看别人项目有什么技能"
- "缺失的技能"
- "还有什么 skill/MCP/插件 我没装的"

### 流程

#### Step 1：收集"可获得"的能力池

扫描以下所有来源，构建完整能力池：

**来源 A — 全局 Skills（`~/.claude/skills/`）**
列出所有全局已安装但当前项目没有的技能。

**来源 B — 已注册项目中的 Skills**
遍历 `~/.claude/skill-registry.json` 中所有已注册项目（排除当前项目），收集它们 `.claude/skills/` 下的技能列表。

**来源 C — 全局 Plugins**
列出 `~/.claude/plugins/` 下已安装但当前项目未启用的插件。

**来源 D — 全局 MCP Servers**
读取 `~/.claude/settings.json` 中的 `mcpServers`，找出全局有但当前项目没有的 MCP。

#### Step 2：对比当前项目，找出缺失项

将可获取的能力池与当前项目已有能力逐一对比：

| 能力 | 当前已有哪些（去重） | 可获取但缺失 |
|------|---------------------|-------------|
| Skills | 项目级 + 全局（已自动可用） | 其他项目的技能、全局未启用的技能 |
| Plugins | `enabledPlugins` 中的 | `~/.claude/plugins/` 中有但未启用的 |
| MCP | 项目 settings 中的 | 全局 settings 中有但项目没有的 |

**注意：** 全局 Skills 已自动对当前项目可用（Claude Code 会自动加载），所以全局 Skills 不需要显示为"缺失"。重点展示：
- 其他已注册项目中的技能（这些不会自动加载）
- 全局已安装但当前项目未启用的插件
- 全局配置了但当前项目没有的 MCP

#### Step 3：格式化展示缺失能力

用中文表格展示所有缺失的能力，标注来源：

```
🔍 发现可获取但当前项目未使用的能力

═══ 🧠 其他项目的 Skills（可导入） ═══
  来源: first-cc (C:/Users/.../first-cc)
    📥 find-skills          — 搜索和发现社区技能
    📥 skill-creator        — 创建、评估和改进技能
    📥 karpathy-guidelines  — 卡帕西编程准则
    📥 yuque-guide          — 语雀插件使用文档

  来源: my-app (C:/Users/.../my-app)
    📥 my-custom-skill      — 自定义技能

═══ 📦 未启用的插件 ═══
    ⬜ content-creator      — 内容创作子代理

═══ 🔌 可添加的 MCP 服务 ═══
  来源: 全局配置 (~/.claude/settings.json)
    ⬜ github-mcp           — GitHub API 集成
```

#### Step 4：交互式选择

展示完毕后，用中文询问用户想添加哪些：

> "发现以上能力可供添加。你想怎么做？"
>
> **可以单个选择：**
> - "导入 first-cc 的 find-skills" — 从指定项目导入指定技能
> - "开启 content-creator 插件" — 在当前项目启用插件
> - "添加 github-mcp" — 添加 MCP 到当前项目
>
> **也可以批量操作：**
> - "全部导入" / "all" — 导入所有缺失的 skills
> - "全部开启" — 启用所有未启用的插件
> - "全选" — 添加所有可获取的能力
>
> **或者：**
> - "跳过" / "完成" — 不做任何操作

#### Step 5：逐一执行

根据用户选择，依次执行每个添加操作。

**导入 Skill 时，对每个 Skill 单独确认：**
> "【find-skills】来自项目【first-cc】— 搜索和发现社区技能。是否导入到当前项目？"
> - 用户确认后复制到 `.claude/skills/`
> - 询问是否永久保留

**启用插件时：**
> "插件【content-creator】已在全局安装但当前项目未启用。是否启用？"
> - 确认后写入 `enabledPlugins`

**添加 MCP 时：**
> "MCP【github-mcp】已在全局配置。是否添加到当前项目？"
> - 确认后写入项目 `.claude/settings.local.json` 的 `mcpServers`

#### Step 6：总结

> "✅ 已完成。本次添加了：
> - Skills: find-skills, skill-creator
> - 插件: content-creator
> - MCP: github-mcp
> 它们已就绪，你现在可以使用了。"

---

## 重要规则

1. **所有操作前先确认** — 绝不自动删除、覆盖或修改，必须征得用户同意
2. **全程使用中文** — 所有提示、询问、展示都用简体中文
3. **路径使用正斜杠** — Windows 路径统一用 `/` 而非 `\`
4. **先读后写** — 编辑任何配置前先读取当前内容
5. **注册表自动维护** — 如果当前项目不在注册表中且用户操作涉及跨项目，提示注册
