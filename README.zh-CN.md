# Agent Rules Skills — 四领域 Skill（主分支）

本分支承载四套已拆分的独立可加载 Skill，按 `SKILL.md` 的 `description` 自动触发；规则库本体（Markdown 规则）已在子分支 `agent-rules-md` 独立管理，两边内容互不干涉。

## 目录结构

```text
skill-trae/
├── skills/
│   ├── math/              # 数学推导、验证、证明、笔记
│   │   ├── SKILL.md
│   │   └── references/optional/   # rigor / pde-galerkin / notes
│   ├── latex/             # LaTeX 编译、排版、安全编辑
│   │   ├── SKILL.md
│   │   └── references/optional/   # compile / typography / beamer
│   ├── coder/             # 编码、审查、Skill/插件开发
│   │   ├── SKILL.md
│   │   ├── references/optional/review.md
│   │   └── GitHub操作手册.md
│   └── calibre/           # 电子书库、元数据、批量写库
│       └── SKILL.md
└── LICENSE                # MIT
```

## 使用方式

1. 安装到 Skill 目录（本机为 `~/.trae-cn/skills/`）后，Trae 通过 `SKILL.md` 的 description 对任务自动触发。
2. 命中某一领域时加载对应 Skill 的 `SKILL.md`，并按需读取其 `references/optional/` 细分纪律。
3. 触发指针精确性：同一任务描述应命中且仅命中一个领域 Skill；邻接场景按核心正确性风险判定主框架。

## 授权

MIT License，见 [LICENSE](LICENSE)。

## 与规则库本体（agent-rules-md）的关系

四套 Skill 与 `agent-rules-md` 子分支共用同一套领域边界与验收标准，Skill 承载可复用纪律，规则库负责路由与边界；内容互不重复维护。
