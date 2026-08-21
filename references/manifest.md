# references/manifest.md — 参考资料加载索引

本文件是规则库 `references/` 的加载清单，把"按需加载"落成可判定结构。判定口诀：**该资料是否改变决策或验收 → 是则常驻（或由路由显式要求）；只影响实现细节 → 按需。**

## 常驻纪律（命中即完整读取，不由本目录文件承载）

规则库总入口 `AGENTS.md` 的以下章节对任何任务都生效，属于常驻纪律：

| 位置 | 内容 | 适用 |
|---|---|---|
| `AGENTS.md` 第三节 | 通用行为准则（任务合同、最小改动、只读边界、证据分级、数据保护、风险相称验证、专业对象定义、临时/正式分离） | 所有任务 |
| `AGENTS.md` 第四节 | 操作风险登记表（高/中/低默认行为、破坏性递归禁令） | 写盘/删除/批量类任务 |

它们与 skill 侧的"常驻纪律"同源，不在此复制，避免重复维护。

## 按需级 `references/`（命中对应领域或子任务才读取）

### 数学领域（主框架 `数学工作者.md`）

| 文件 | 适用子任务 | 触发说明 |
|---|---|---|
| [math/rigor.md](math/rigor.md) | 证明、边界、独立验证、第一性分析 | 任意 Math 主任务通常必读 |
| [math/workflows.md](math/workflows.md) | 一般工作流、任务组织 | 多步数学任务 |
| [math/validators.md](math/validators.md) | 独立验证、残差检查 | 涉及计算或推导验证 |
| [math/numerics.md](math/numerics.md) | SymPy/NumPy/SciPy 数值实验或绘图 | 含代码或图形的数学任务 |
| [math/pde-galerkin.md](math/pde-galerkin.md) | PDE/Galerkin/正交基/坐标映射 | 低频专用，由路由显式命中 |
| [math/note-design.md](math/note-design.md) | Math 笔记、错题、知识体系 | Markdown/LaTeX 笔记任务 |
| [math/training.md](math/training.md) | 训练、掌握度测试、知识漏洞检测 | 用户要求训练模式时 |

### LaTeX 领域（主框架 `Latex排版助手.md`）

| 文件 | 适用子任务 | 触发说明 |
|---|---|---|
| [latex/compile.md](latex/compile.md) | 编译失败、工具链、缓存 | 由 latex 框架路由 |
| [latex/math-typography.md](latex/math-typography.md) | 公式/符号/上下标/数学字体 | 由 latex 框架路由 |
| [latex/safe-editing.md](latex/safe-editing.md) | 批量替换、结构性编辑 | 由 latex 框架路由 |
| [latex/beamer.md](latex/beamer.md) | Beamer 幻灯片结构/溢出 | 由 latex 框架路由 |

## 加载流程

1. 用户显式指定规则库或 `AGENTS.md` 后，完整读取 `AGENTS.md`（含常驻纪律）。
2. 按路由表确定主框架；主框架内命中哪类子任务，就读取对应子任务列出的参考文件。
3. 未命中任何领域或子任务时，不加载任何按需资料；混合任务只为明确子任务加载辅助框架对应参考。
4. 修改路由表后，按 `AGENTS.md` 维护纪律重跑 `evals/routing-cases.md` 做回归。
