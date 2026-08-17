---
when_to_use:
  - 编写或修改代码、实现软件功能和修复缺陷
  - 代码审查、diff 或 commit 检查
  - 创建或安装 Skill、创建插件、查询 OpenAI 或 Codex 官方文档
  - 生成或编辑位图素材
do_not_use:
  - 以数学结论为目标的推导或数值验证
  - 纯 LaTeX 工程与排版任务
  - Calibre 书库写入和元数据管理
version: 1.2.0
last_updated: 2026-08-17
---
# Coder — 编码与代码生态框架

本文件只规定个人路由、边界和验收；创建、安装或调用 Codex Skill 与插件时，以当前可用的官方或已安装能力为执行规范，不在这里复制易过期的接口细节。

## 定位

处理编码与 Codex 能力生态任务。项目本身的结构、接口、测试和局部规则优先于本框架的默认建议。

## 编码准则

1. 先读取项目结构、项目级规则、相关实现和测试，再决定修改点。
2. 诊断和审查默认只读；实现或修复任务才编辑文件。
3. 只修改目标所需内容，不格式化、重构或更新无关文件。
4. 保留现有接口、用户改动、依赖管理和工具链，除非任务明确要求改变。
5. 新增依赖必须具有明确必要性，并采用项目已有管理方式。
6. 验证范围与风险相称：从相关静态检查和局部测试开始，必要时扩大到完整测试。
7. 最终报告变更、验证结果、未覆盖范围和残余风险。

## 能力模块（路由表）

| 模块 | 用途 | 对应源技能 / 动作 |
|---|---|---|
| 写代码 | 按需求实现或修改代码 | 读取项目 → 实现最小改动 → 运行相关验证 |
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

推荐结构以当前 `skill-creator` 为准，通常采用渐进式加载：

```text
skill/
├── SKILL.md          # frontmatter + 模块路由表（场景 → 命令）
├── scripts/          # 可执行脚本（Python 等）
├── references/       # 分类知识库（模块详解/规则）
└── evals/            # 回归测试 + fixtures
```

模块路由表约定：`$SKILL_DIR` 指 skill 安装目录，执行前替换为实际路径。

## 代码审查

审查保持只读，以会影响正确性、安全性、性能或可维护性的具体缺陷为先。每条发现说明触发条件、影响、证据位置和可执行修复方向；没有实质性发现时明确说明验证范围，不用风格偏好填充清单。

## 边界

- 审查只读，未经确认不直接改代码；
- 安装技能先做安全检查（读 SKILL.md 全部内容，有风险先警告）；
- 安装 Codex skill 时遵循当前 `skill-installer` 的实际目标目录，不使用其他 agent 生态的路径或约定；
- 生图仅输出位图资产，矢量/图标/HTML/CSS 场景不做。
- Git 与 GitHub 的仓库维护细节见 `GitHub操作手册.md`，不在本框架重复维护。
