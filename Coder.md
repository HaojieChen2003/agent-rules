---
when_to_use: 写代码/改代码/代码审查/review/diff/commit/创建技能/安装技能/创建插件/查询 OpenAI 或 Codex 文档/生成位图素材
do_not_use: 数学推导与数值验证（数学工作者.md）；LaTeX 排版（Latex排版助手.md）；书库管理（Calibre书库管理.md）
version: 1.1.0
last_updated: 2026-08-05
---
# Coder — 编码与代码生态框架

> 复刻自 codex 技能体系：`review-agent` / `skill-creator` / `plugin-creator` / `skill-installer` / `openai-docs` / `imagegen`。本文件是路由入口，源技能（`~/.codex/skills/`）是执行引擎。

## 定位

写代码、代码审查、创建/安装技能与插件、查询官方文档、AI 生图。

**何时用**：用户要求写/改代码、审查代码变更、创建或更新 skill/插件、安装技能、查询 OpenAI/Codex 文档、生成位图素材。

## 能力模块（路由表）

| 模块 | 用途 | 对应源技能 / 动作 |
|---|---|---|
| 写代码 | 按需求实现/修改代码，遵循项目约定 | 直接执行（Read 项目结构 → 写 → 验证）|
| 代码审查 | 对未提交变更 / diff / commit 做**只读缺陷优先**审查 | `review-agent`（返回每个可执行的发现）|
| 创建技能 | 造新 skill 或更新现有 skill | `skill-creator`（frontmatter 规范见下）|
| 创建插件 | 造 Codex 插件（`plugin.json` + marketplace）| `plugin-creator` |
| 安装技能 | 从精选列表 / GitHub 仓库安装技能 | `skill-installer` |
| 文档查询 | 查 OpenAI 产品 / API / Codex 官方文档（带引用）| `openai-docs` |
| 图像生成 | AI 生成/编辑位图（照片/插画/素材/mockup）| `imagegen`（输出位图，非矢量/代码原生）|

## 技能与个人框架规范

Codex `SKILL.md` 与本目录的个人框架是两套不同接口，不混用 frontmatter：

- 创建或更新 Codex skill 时，必须完整读取当前 `skill-creator`，以其最新规范为准；不要在本文件静态复制可能过期的 schema。
- 本目录领域框架使用 `when_to_use`、`do_not_use`、`version`、`last_updated`，由 `AGENTS.md` 在用户显式加载后解释，不宣称这些字段是 Codex skill 的系统触发字段。

当前个人框架 frontmatter：

```yaml
---
when_to_use: 触发条件
do_not_use: 排除条件
version: 语义化版本
last_updated: YYYY-MM-DD
---
```

推荐结构（参考 `latex-thesis-zh`）：

```text
skill/
├── SKILL.md          # frontmatter + 模块路由表（场景 → 命令）
├── scripts/          # 可执行脚本（Python 等）
├── references/       # 分类知识库（模块详解/规则）
└── evals/            # 回归测试 + fixtures
```

模块路由表约定：`$SKILL_DIR` 指 skill 安装目录，执行前替换为实际路径。

## 代码审查流程（review-agent 模式）

```text
1. 只读（不修改代码）
2. 缺陷优先：每一条都给出可执行的修复建议
3. 审查对象：未提交变更 / base 分支 diff / commit / 自定义审查指令
4. 返回结构化清单，不泛泛而谈
```

## 边界

- 审查只读，未经确认不直接改代码；
- 安装技能先做安全检查（读 SKILL.md 全部内容，有风险先警告）；
- 安装 Codex skill 时遵循当前 `skill-installer` 的实际目标目录，不使用其他 agent 生态的路径或约定；
- 生图仅输出位图资产，矢量/图标/HTML/CSS 场景不做。
