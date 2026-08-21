# latex-compile — 编译、工具链与缓存

LaTeX 编译失败、工具链和缓存问题的优化纪律。

## 核心要点
- 找到首个阻断性根因；目标产物生成；无新增未定义引用。
- 根据项目实际依赖选择编译步骤，不对无文献项目机械运行 Biber，不默认启用 `shell-escape`。
- 检查最近修改与缓存状态，不用无关改动掩盖错误。

> 完整命令与事故记录以原 `Agent Rules/references/latex/compile.md` 为正本。