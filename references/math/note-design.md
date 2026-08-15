# 数学笔记设计

## 1. 组件化原则

笔记用于未来快速重建知识，不保存全部对话过程，也不机械拥有相同章节。按用途选择：

```yaml
required:
  - summary
  - assumptions_or_definition
  - key_result
optional:
  - intuition
  - proof
  - examples
  - counterexample
  - python
  - mistakes
  - links
  - quiz
```

## 2. 概念笔记

按需包含：一句话理解、严格定义、适用范围、直觉、正例、反例或边界、相近概念区别、判断流程和自测题。

## 3. 定理与证明笔记

按需包含：前置定义、定理条件、结论、条件必要性、证明策略、关键步骤、容易失败的步骤和可迁移证明模式。

不得只保存漂亮的最终证明而丢失关键合法性；机械计算可以折叠，逻辑依赖不能折叠。

## 4. 错题笔记

重点保存错误机制：

```markdown
# 错题：关键词

## 原题
## 我的错误答案
## 首个错误位置
## 错误类型
## 根本原因
## 正确解法
## 更稳健的判断方法
## 同结构变式
## 下次如何避免
```

错误类型包括定义不清、条件遗漏、公式误用、分类遗漏、逻辑跳步、符号混乱、计算错误和编程实现错误。

## 5. 知识沉淀

高价值任务结束后按需沉淀为：

- 概念卡片；
- 定理卡片；
- 证明模式；
- 方法模板；
- 错误机制；
- 反例库；
- Python 实验；
- 符号约定；
- 知识依赖链接。

区分当前回答、可复用知识、文件产物和仍未掌握的问题。

## 6. Markdown 与 LaTeX

- Markdown 行内公式使用 `$...$`，独立公式使用 `$$...$$`；兼容 Typora 时按项目现有风格处理 TOC、高亮、任务列表和表格。
- `.tex` 行内优先 `\(...\)`，无编号独立公式优先 `\[...\]`；需要编号或引用时使用 `equation`、`align` 等环境。
- 一个整体编号、内部多行对齐时使用 `equation + aligned`。
- 保留项目已有宏、标签、引用键和设计语言，不为统一风格大规模重写。

## 7. 数学笔记模板

在 TexStudio 工作区新建 macOS 风格数学笔记时，若存在下列模板则优先复用：

```text
/Users/eiravale/Desktop/TexStudio/Templates/TemplateMathNotesMacStyle/MathNotesTemplate.tex
/Users/eiravale/Desktop/TexStudio/Templates/TemplateMathNotesMacStyle/references.bib
```

保留公开接口：

```latex
\defn{标题}{内容}
\prop[标题]{内容}
\pf{题目}{证明步骤}
\conclusion[标题]{内容}
\note[标题]{内容}
\unresolved[标题]{内容}
```

普通数学学习笔记不得套用学位论文结构、盲审规则或学校规范。
