# Agent Rules — 个人领域行为准则库

这是一套面向 AI Agent 的、可版本化的个人领域规则。它不复制完整操作手册，而是沉淀会长期影响判断、执行边界和验收方式的准则。

## 设计原则

- **入口简洁**：`AGENTS.md` 只负责作用域、优先级、路由和跨领域边界。
- **领域分离**：数学、LaTeX、编码和书库任务由独立框架主导。
- **按需加载**：教程、命令、案例和软件细节放入 `references/` 或 `evals/`；`references/manifest.md` 提供加载索引。
- **单一事实来源**：外部 Skill 或官方文档已有的易变信息，不在本库重复维护。
- **风险可控**：危险操作先核对目标和恢复方式，验证与任务风险相称。
- **可演化**：规则使用 Git 管理，但历史状态和命令细节不进入行为入口。

## 目录结构

```text
Agent Rules/
├── AGENTS.md              # 总入口：激活、路由、通用行为和风险边界
├── 数学工作者.md          # 数学推导、证明、验证与知识沉淀
├── Latex排版助手.md       # LaTeX 工程、安全编辑、编译与验收
├── Calibre书库管理.md     # 电子书库、元数据和批量写入
├── Coder.md               # 编码与 Codex 能力路由
├── GitHub操作手册.md      # 本规则库的 Git/GitHub 维护流程
├── references/            # 按任务读取的稳定方法和领域细节
│   ├── manifest.md        # 加载索引：常驻纪律与按需参考的挂载清单
│   ├── math/              # 数学领域细分参考（rigor/workflows/numerics 等）
│   └── latex/             # LaTeX 细分参考（compile/typography/safe-editing 等）
├── evals/                 # 路由、边界和领域行为回归案例
└── skills/                # 四领域独立 skill（math/latex/coder/calibre）
    ├── math/              # SKILL.md + references + evals
    ├── latex/             # SKILL.md + references + evals
    ├── coder/             # SKILL.md + references + GitHub操作手册
    └── calibre/           # SKILL.md

```

## 使用方式

1. 用户显式指定本目录或 `AGENTS.md` 后，先读取总入口；`AGENTS.md` 第三节与第四节为常驻纪律，任何任务命中即生效。
2. 根据核心任务的触发指针选择一个主框架；混合任务只为明确子任务加载辅助框架。
3. 通过 `references/manifest.md` 定位并按需读取与当前子任务相关的参考资料，不加载无关模块。
4. 按框架的最低验收标准报告结果、不确定性和残余风险。

## 回归验证

改动路由表、触发指针或任一领域框架的安全/验收边界后，重跑 `evals/` 对应用例（路由见 `routing-cases.md`，安全边界见 `safety-cases.md`），确保行为未漂移。

## Skills

`skills/` 下是与规则库同源的独立可加载 Skill（math/latex/coder/calibre），
按 `SKILL.md` 的 description 自动触发。规则库本体（AGENTS + 领域框架）
负责路由与边界，Skill 承载可复用纪律；两者共用同一套领域边界与验收标准。

本目录的 frontmatter 是个人规则协议，不是 Codex 原生 Skill 的系统触发接口。真正的 Skill 仍以其当前 `SKILL.md` 为执行规范。
