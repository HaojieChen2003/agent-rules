---
when_to_use: LaTeX/XeLaTeX/TeXstudio/.tex 文件/学位论文/毕业论文/编译失败/GB/T 7714/参考文献/公式断行/术语一致性/去AI味/盲审/学校规范终检
do_not_use: 英文论文（走 latex-paper-en）、Typst 项目、只有 DOCX/PDF 无 LaTeX 源、纯文献调研、从零写论文
version: 1.7.0
last_updated: 2026-08-15
---
# Latex排版助手 — 中文 LaTeX 排版框架

> 复刻自 `~/.codex/skills/latex-thesis-zh`（源 skill 是执行引擎，本文件是路由入口）。

## 定位

处理**已有中文 `.tex` 项目**中的定向问题：编译诊断、格式与 GB/T 7714、公式编号与断行、章节结构、模板识别、术语一致性、逻辑与文献综述、标题优化、去 AI 味、盲审隐匿、对照学校规范逐项终检。

**何时用**：编译失败、工具链不确定；格式/国标/学校模板检查；公式编号挤到下一行、长公式拆行；章节结构、术语/缩略语不一致；逻辑连贯性、文献综述、导语完整性、标题架构；绪论漏斗、章引言、本章小结；标题优化、去 AI 化；定稿终检、盲审版生成。

**何时不用**：英文论文（走 latex-paper-en）、Typst 项目、只有 DOCX/PDF 无 LaTeX 源、纯文献调研、从零写一篇学位论文。

## 与数学工作者的接口

- 数学结论、证明、符号推导和数值验证由 `数学工作者.md` 主导；本框架不以“能够编译”替代数学正确性。
- 纯编译、公式环境、引用、版面和论文结构问题由本框架主导。
- 数学内容写入 `.tex` 时，先完成数学内容验收，再由本框架写入、编译并检查 PDF；分别报告数学与 LaTeX 验收结果。
- 普通数学学习笔记不得因使用 `.tex` 而套用学位论文结构、盲审或学校规范。

## 环境默认值

| 项 | 默认 |
|---|---|
| 编辑器 | TeXstudio |
| 编译器 | 项目已有工具链优先；无既有约定时使用 XeLaTeX |
| 文献系统 | 项目已有系统优先；使用 biblatex 时采用 Biber（`\cite` / `\printbibliography`）|
| 中文支持 | `ctex` / `ctexart` / `ctexbook`（不重复加载），`ctex` 置于中文相关定义之前 |
| shell-escape | 默认关闭，仅用户明确确认才启用 |
| 字体 | 保留项目已有字体选择，不擅自更换系统字体 |

## 编译流程

- 不含文献：XeLaTeX → XeLaTeX（稳定目录/交叉引用）
- 含文献（biblatex）：XeLaTeX → Biber → XeLaTeX → XeLaTeX
- 不得对无文献文档机械执行 Biber
- **增量编译优化（用户偏好，耦合系统项目）**：日常迭代只跑 XeLaTeX → XeLaTeX，**不跑 Biber**（biber 慢）。仅当满足以下任一条件才补 Biber：
  1. 无 `.bbl` 文件；
  2. 新增/删除 `\cite`/`\textcite`/`\citeauthor`；
  3. `refs.bib` 条目发生变化；
  4. 编译日志出现 `Citation '...' undefined`。
  判断方法：对比源文件活跃区的 cite 键集合与 `.bbl` 的 `\entry` 键集合，一致则无需 Biber。**编译后保留 `.bbl`（不清理）**，避免下次重复 Biber；`.aux`/`.log`/`.bcf`/`.run.xml` 等仍清理。
- **编译失败时**：优先定位第一个阻断性错误，用最小改动修复，不用无关改动掩盖问题
- **缓存污染**：注释掉宏包（如 biblatex）或改文档类后，旧的 `.aux`/`.bcf` 残留其命令（如 `\abx@aux@...`）→ 编译报"未定义"——**先删 `.aux .bcf .log` 再编译**；报错可能是缓存不是代码。

## 批量脚本替换规范（Python 批量修改 .tex）

> 2026-08-15 中英迁移/符号统一/label 改名实战沉淀。批量替换比逐条 Edit 高效，但必须防三类事故：**转义错、二次替换、误伤命令**。

1. **`re.sub` 替换串转义**：替换为 `\mathrm d` 必须写 `r'\\mathrm d'`（`\\`→`\`）；写 `r'\mathrm d'` 会报 `bad escape \m`；
2. **占位符保护法（防二次替换）**：先 `src.replace(r'\mathrm d', '@@XD@@')` 保护已正确内容，替换完再还原 `@@XD@@` → `\mathrm d`；负向后顾（`(?<!\mathrm)`）在 `\,\mathrm d` 等带前缀场景会失效导致二次污染，占位符法最稳；
3. **`{旧名}` 花括号边界匹配**：改 label 用 `'{' + old + '}'` 精确匹配（如 `{fl:phi}`），并按旧名长度降序替换，防前缀子串误伤（`fl:phi` vs `fl:phi-phi-omega1`）；
4. **tikz 保护**：全文替换前把 `tikzpicture` 块替换为占位符再还原（`\draw`、`draw=macTeal!55` 等会被"裸 d"类规则误伤，且错误报在别处极难定位）；
5. **写盘前校验**：脚本先 `assert old in src` / 统计未命中并打印，命中数不符就不写盘，避免半替换落盘；
6. **替换后复查**：编译验证零警告 + 残留扫描（中文正则或英文词表），确认目标完全清零。

## 诊断方法论（第一报错行 ≠ 根因）

- `\mathrm draw` 写在 L190，报错却出现在 L2041 的 `}`——批量替换误伤常在远离现场处爆炸；先怀疑"最近一次全局修改"；
- `Too many }'s` 报在 `\end{frame}`：先查 `\begin{frame}` 配对，再查内容；
- `Missing $ inserted` 在空行处：数学模式内空行（equation 后空行）——删空行；
- 存疑时用**最小复现**：写 3 行独立测试文档编译，排除主题/宏包干扰（如 beamer"竖杠"= Madrid 罗马数字帧编号）；
- PDF 诊断：pypdf 提取文本、sips 转 PNG 目检；
- **停用区（`\iffalse`）label 不可引用**：活跃区引用停用区 label → undefined；要么本节重写，要么等恢复；
- **行号是漂移的**：用户并行编辑后行号全变——用标记字符串定位，不用行号；编辑遇到 "File has been modified" 必须重读最新内容。

## Beamer 定理环境

- Beamer 已内置的定理环境不得重复用 `\newtheorem` 声明；
- `ctex` 负责相应环境名称的中文化；
- 只有项目确需新增、且名称不与内置环境冲突时，才定义新的定理类环境。

## Beamer 幻灯片排版（周报实战总结）

> 2026-08-15 周报多轮修复沉淀；适用于 `WeeklyReports/` 下的 beamer 周报与通用 beamer 幻灯片。

### Frame 结构铁律

1. 所有可见内容必须在 `\begin{frame}...\end{frame}` 内；`\end{frame}` 外的内容不显示；
2. `\end{document}` 永远在文件最后——新加的 frame 若写在它后面整帧不编译；
3. 帧标签配对：缺 `\begin{frame}` 只剩 `\end{frame}` → `Too many }'s`（错误报在 `\end{frame}` 处，先查配对再查内容）；
4. 长内容（推导 + 结果超一页）→ 帧加 `[allowframebreaks]` 自动分页（标题每页重复）；或手动拆帧（标题更清晰）；
5. 帧内小标题用 `\textbf{...}`，不要用 `\paragraph/\subparagraph`（beamer 未定义 → `Undefined control sequence`）。

### 数学模式规范（beamer 内）

1. `\begin{equation}` 后 / `\end{aligned}` 后不能有空行——数学模式内空行 → `Missing $ inserted`；
2. 多行公式一律 `equation + aligned`，裸 `&`/`\\` → `Misplaced alignment tab`；
3. `cases/aligned` 内大算符加 `\displaystyle`（或 `dcases`）；
4. 帧标题可直接用数学对象（如 `\begin{frame}{$<...>$}`），但公式后的"竖杠"不是 bug——见下方主题装饰；
5. 公式超宽：先拆行，再 `\small`，最后 `\footnotesize`；超高（`Overfull \vbox`）用 `[allowframebreaks]`。

### 主题与装饰

- Madrid 主题 frametitle 右侧自动显示**罗马数字帧编号**（第 1 帧 = I、第 5 帧 = V），纯文本标题也有——不是 bug；想去掉：换主题（Warsaw/Berlin/Copenhagen 等）或自定义 frametitle 模板；
- 去掉右下角导航符号：`\setbeamertemplate{navigation symbols}{}`；
- 纯英文文档不要加载 ctex；需要中文则完整加载 ctex + 中文字体，不要中英混排。

### 编译与缓存

1. 注释掉宏包（如 biblatex）后，旧的 `.aux`/`.bcf` 残留其命令（`\abx@aux@...`）→ 编译报未定义——**先删 `.aux .bcf .log` 再编译**：报错可能是缓存不是代码；
2. biblatex 加载但全文无 `\cite` → biber 报 "no citations"——不用文献就注释掉 biblatex 两行；
3. 标准流程：XeLaTeX ×2（引用稳定）；有文献补 Biber；
4. 日期/作者信息（`\date`/`\author`）每次汇报前更新。

### 验收

- 编译：零 `!` 错误、零 undefined、零 Overfull、零中文残留（grep 中文）；
- 结构：frame 配对、`\end{document}` 在文件尾；
- 清理：`.aux .log .bcf .run.xml .toc .nav .snm .out .blg .synctex.gz`，只留 `.tex` + `.pdf`。

## 能力模块（路由表）

命令约定：`$SKILL_DIR = ~/.codex/skills/latex-thesis-zh`，`main.tex` 按实际路径替换。

| 模块 | 触发场景 | 命令 |
|---|---|---|
| compile | 编译失败 / 工具链不确定 | `uv run python $SKILL_DIR/scripts/compile.py main.tex` |
| format | 格式、公式布局/断行、GB/T 7714 版式、占位符泄漏 | `uv run python $SKILL_DIR/scripts/check_format.py main.tex` |
| structure | 章节结构总览 / 骨架 | `uv run python $SKILL_DIR/scripts/map_structure.py main.tex` |
| consistency | 术语/缩略语/命名跨章漂移 | `uv run python $SKILL_DIR/scripts/check_consistency.py main.tex --terms` |
| template | 识别/校验文档类与模板（thuthesis/pkuthss/generic）| `uv run python $SKILL_DIR/scripts/detect_template.py main.tex` |
| bibliography | GB/T 7714 / BibTeX 校验 | `uv run python $SKILL_DIR/scripts/verify_bib.py references.bib --standard gb7714` |
| logic | 章节逻辑连贯性、导语完整性 | `uv run python $SKILL_DIR/scripts/analyze_logic.py main.tex` |
| literature | 文献综述（共识→分歧→局限→空白→切入点）| `uv run python $SKILL_DIR/scripts/analyze_literature.py main.tex` |
| experiment | 实验章语言、讨论分层、结论完整性 | `uv run python $SKILL_DIR/scripts/analyze_experiment.py main.tex` |
| references | 交叉引用完整性（未定义 \ref、编号缺口）| `uv run python $SKILL_DIR/scripts/check_references.py main.tex` |
| tables | 表格结构、三线表、booktabs | `uv run python $SKILL_DIR/scripts/check_tables.py main.tex` |
| abstract | 摘要骨架诊断、中英一致性、字数校验 | `uv run python $SKILL_DIR/scripts/analyze_abstract.py main.tex` |
| conclusion | 结论三段式（总结/创新/展望）、空话检查 | `uv run python $SKILL_DIR/scripts/analyze_conclusion.py main.tex` |
| deai | 去 AI 味（草稿注记/委婉语/占位符）| `uv run python $SKILL_DIR/scripts/deai_check.py main.tex` |
| spec-check | 定稿对照学校规范逐项终检 | `uv run python $SKILL_DIR/scripts/check_spec.py main.tex --template yanshan --degree doctor` |
| blind-review | 盲审个人信息隐匿与盲审版生成 | `uv run python $SKILL_DIR/scripts/blind_review.py main.tex --check` |

## 公式环境规则（equation → aligned）

- 若 `\begin{equation}...\end{equation}` 内包含多个方程，且出现 `&` 与 `\\` 符号，用户意图是**对齐与换行** → 直接用 `aligned` 环境修正（改为 `\begin{equation}\begin{aligned}...\end{aligned}\end{equation}` 结构），**直接改正，无需询问**。
- 此写法的目的是表示**一个方程**（整体是一个编号公式），故 `\label` 作用于整个 `equation` 环境（整体），**不对应其中某一个方程**。

### 环境误用高频模式（耦合系统 V6/V7 实战总结，按出现频率排序）

以下 5 种模式在手工粘贴/改写公式块时反复出现，前 3 种是**编译错误级**（`Misplaced alignment tab`、`Missing }`、`Extra \right`），后 2 种会**静默吞内容**（编译通过但 PDF 缺段、biber 丢引用）：

1. **`\boxed{}` 内裸用 `&`/`\\`**：`\boxed{ A &= B \\ C &= D }` 不合法，必须包 `\begin{aligned}`，即 `\boxed{\begin{aligned}...\end{aligned}}`；
2. **`\pf{题目}{内容}` 的题目参数里嵌 display 环境或裸数学 + `&`/`\\`**：题目是水平模式（tcolorbox 标题栏），`^` 触发 `Missing $`、`&` 触发 `Misplaced alignment tab`，错误还会滞后报在内容参数的 `}` 处，极难定位。题目只放短公式（`$...$` 包裹或 `\eqref`），长公式放内容参数；
3. **`\left\langle`/`\left[` 跨 `&` 对齐点**：`\left` 与 `\right` 必须成对且不跨 `&`，跨对齐点会报 `Extra \right`/`Missing }`。需要跨行括号时用 `\left[ ... \right.` + `\left. ... \right]` 或 `aligned` 内 `\Biggl[ ... \\ & ... \Biggr]`；
4. **`\begin{equation}` 缺配对 `\end{equation}`**（环境横穿 `\pf`/`\boxed` 边界）：编译不一定立即报错，但 tcolorbox 强制收尾会**吞掉其后所有内容**——现象是 PDF 缺 section、`.aux` 缺 `\citation`、biber "Found 1 citekeys"。排查"内容莫名消失、参考文献不全"时必须先查环境配对；
5. **`\begin{equation}` 未闭合 + 嵌套环境计数**：`\pf` 内容里的 `equation` 若缺 `\end`，闭合括号会被误认为 `\pf` 的收尾，后续全部静默丢失。
6. **数学对齐环境内部空行**（`aligned`/`align`/`cases`/`dcases`/`split` 内的空行）：空行即 `\par`，对齐环境只认 `\\` 分行的行状态，遇 `\par` 直接崩——现象是 `\end{frame}` 处**一串** `Missing $`/`Missing }`/`Missing \cr`/`Misplaced \crcr`/`Extra }` 连环报错，错误滞后不指真病根，且内容被吞（页数骤减，如 11→6）。2026-08-15 实战：`\label` 前残留空行导致 9 个错误 + 吞 5 页。修复：删公式块内所有空行；排查可用脚本扫描数学环境内的空行。

**排查顺序**：先跑一次编译看第一个 `!` 错误 → **若错误成串且滞后报在 `\end{frame}`（Missing $/Missing }/Missing \cr 连环），先查数学对齐环境（aligned/cases/split）内空行** → 若编译通过但页数异常减少/内容缺失，按顺序查 `\iffalse`/`\fi` 配对 → 查 tcolorbox（`\pf`/`\boxed`）内环境闭合 → 查 `.aux` 的 `\citation` 是否完整。

## 大算符显示样式（display style）

- **用户偏好（硬性）**：求和 `\sum`、积分 `\int` 等大算符的上下限默认**堆叠显示**——上面的在符号正上方，下面的在符号正下方；不使用紧凑的 text style（上限右上、下限下方）。
- 独立展示公式（`equation`/`\[...\]`/`align` 等 display 环境）默认已是堆叠样式，无需处理；
- `cases`、`aligned`、`split` 等环境默认按**行内数学模式**排，其中的 `\sum`/`\int` 会退化为紧凑样式 → 在该行加 `\displaystyle`（等价做法：用 mathtools 的 `\begin{dcases}` 代替 `\begin{cases}`，整环境自动 display style）；
- 验收：PDF 中 `\sum_{n=1}^{N}` 与 `\int_a^b` 的上下限必须堆叠在符号正上下方。

## 符号首次使用与显式性

- 在公式首次使用非标准符号、函数、Green 核、算子、矩阵、系数或缩写之前，先给出其参数、定义域和数学定义。
- 若该对象已有可直接计算的显式表达式，必须在首次进入核心公式前完整排出；不得仅以"见文献"、指向后文章节的交叉引用或未解释的名称代替。
- 长定义可以独立设置 `\paragraph`、`\subparagraph` 或定义环境，但文档顺序必须保持"定义 → 使用"；编译通过不能替代这项内容完整性检查。

## 高频问题清单（项目实战经验）

按"边推边写"状态下中文 LaTeX 文档（如耦合系统 V6/V7）的实际修改记录统计，以下问题按出现频率排序：

| 排名 | 问题 | 出现频率 | 处理方式 |
|---|---|---|---|
| 1 | **中英混排 / 草稿语言** | 最高（每轮都有，~17 处/文档） | **语言偏好（2026-08-13 用户明确）：文档以英文为主。** 用户写英文 → 润色为通顺英文（修正语法/拼写/时态/冠词/大小写），**严禁删英文或替换成中文**；仅当用户明确写中文时才保留中文。专有名词（Galerkin/Green/Neumann/Fourier 等）正常嵌入，不动。**改格式任务（2026-08-15 用户明确）：用户说"改格式"时，若文档中发现中文内容，顺手翻译为英文，无需单独请示** |
| 2 | **多行公式环境误用**（见上一节） | 高（~6 次，其中 4 次编译错误级） | 按"公式环境规则"小节直接修正 |
| 3 | **硬编码节号**（"第~X.X 节"写死） | 高（~11 处） | 一律改 `\ref{...}`，缺失的 section 补 `\label`；跨文档历史引用（"v6 的第~2.2 节"）改为文字表述 |
| 4 | **label 管理** | 中（~4 处） | 重复 label（同键两处定义）删多余；**被引用但从未定义的 label**（`undefined reference`）常被更早的编译错误掩盖——先修完编译错误，再重新编译查 undefined，最后补 `\label` |
| 5 | 零碎笔误 | 中（~7 处） | `\quad` 缺反斜杠、公式外孤立字符（如行尾多一个 `x`）、`\label{...},\\` 后跟孤立 `\\`、数学符号忘加 `$`（如 `(k)`、`G`）、`\mathrm`/`\text` 缺失导致的斜体符号 |
| 6 | **Overfull \hbox**（公式超宽） | 低（1-2 处） | 先 `\small` 压，仍超宽改 `\footnotesize`；对并排 `array`（如 Singular/Regular 两列）最有效 |
| 7 | **`\iffalse` 停用区** | 低（但破坏力最大） | 现象：PDF 页数减半、参考文献只剩 1 条、`.aux` 只有 1 个 `\citation`。排查内容/文献"莫名缺失"时**首先**查 `\iffalse`/`\fi` 配对，确认是故意停用还是误留 |
| 8 | **`\small`/`\footnotesize` 写在数学模式内** | 低 | 现象：TeXstudio 警告 `Command \footnotesize invalid in math mode`（xelatex 不报 `!` 错误）。字号命令写在 `\begin{equation}` 之后即处于数学模式，无效。改为 `{\footnotesize \begin{equation}...\end{equation}}`——字号声明放环境前（文本模式），用 `{}` 组限定作用域，不污染后续文本 |
| 9 | **文本模式裸 `_`**（如 `phi_k,m`、`c_t` 忘了包 `$`） | 低（但报错迷惑） | `_` 只在数学模式有效；文本模式下触发 `Missing $`，且报错位置滞后到 `\end{frame}`。一律写成 `$\phi_{k,m}$`；display 环境外的普通文本里出现下划线即嫌疑 |

### 预防建议（写给作者）

1. 改完公式立刻编译（TeXstudio 一键），`Misplaced alignment tab` 当场暴露；
2. 多行公式直接用 `\begin{align}` 或编辑器公式模板，不要手写 `&`/`\\` 再回忆是否包了 `aligned`；
3. 段落标题、`\note`、`\subsection` 标题是草稿语言重灾区，发布前统一过一遍；
4. 节号一律 `\ref`；
5. 发布前最后一遍：全量编译 → 零 `!` 错误 → 零 `undefined` 引用 → 零 Overfull → 清理辅助文件只留 `.tex` + `.pdf`。

### 验收附加项（内容完整性，防"静默吞内容"）

编译通过 ≠ 内容完整。页数异常减少、某 section 消失、参考文献条目数不对时，用以下证据链定位：`.aux` 的 `\citation` 键数 → `.bbl` 的 `\bibitem` 数 → `\iffalse`/`\fi` 行号配对 → tcolorbox 内 `equation` 闭合。任何一步对不上，先查环境，不要直接怀疑内容。

## 流程

1. 读请求 → 判断内容正确性或 LaTeX 工程哪一项是主风险，再匹配最小模块；
2. 运行对应脚本（`uv run python …`）；
3. 按 `references/` 对应模块文档解释输出；
4. 返回问题 + 建议（论文审阅友好格式）；用户明确要求修改指定 `.tex` 时视为已授权，不重复确认；
5. 修改后验收：编译命令退出状态正常、目标 PDF 成功生成；无新增未定义引用/标签/文献警告；自定义命令、数学环境、引用键和目录结构未被意外破坏；图形、代码和编译产物位于当前项目内；
6. 最终向用户说明：修改内容、验证方式、编译结果及仍存在的警告。

## 项目规范（TexStudio 工作区）

- 学习笔记主目录：`/Users/eiravale/Desktop/TexStudio/LearningNotes/`，每个项目独立文件夹，编译产物不得跨项目存放；
- 需要代码/图片/数据/生成结果的项目，在项目目录内创建 `项目名_CodeAndPic/`，相关产物统一放入（已有其他明确结构时保持原结构）；
- 新建项目先参考 `/Users/eiravale/Desktop/TexStudio/Templates/` 中对应模板（每周汇报 / 论文阅读 / 数学笔记 / 通用 Beamer / 作业习题）；修改已有项目以当前项目风格为主，不为套用模板大规模重写；
- 默认学术蓝灰色调：主暗色深海军蓝 `RGB(28,40,65)`、强调色钢蓝 `RGB(55,100,150)`、背景色冷灰白 `RGB(243,246,250)`、警示色低调赤陶 `RGB(190,95,70)`；已有项目具有明确配色时保持现有配色。

## 边界

- 全程不破坏引用、标签和数学环境；
- 编译产物不得写入其他项目目录；
- 修改已有项目保持其原有设计语言，不套模板大规模重写；
- 图形方案（TikZ vs Python）需先询问用户，不默认。
- 项目构建文件遵循项目工具链，不因全局临时文件规则被强制搬离项目；任务外的一次性分析产物才使用独立临时目录。
