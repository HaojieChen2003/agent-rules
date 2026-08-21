# Agent Rules — Personal Domain Rules Base

**Language:** [English](README.md) | [中文](README.zh-CN.md)

A versioned, personal set of domain rules for AI agents. It does not duplicate full manuals; it distills the guidelines that consistently shape judgment, execution boundaries, and acceptance standards.
This branch is the **rule base itself** — Markdown rules and references only, no Skill implementations.

## Design Principles

- **Simple entry**: `AGENTS.md` handles only scope, priority, routing, and cross-domain boundaries.
- **Domain separation**: math, LaTeX, coding, and e-book library tasks are led by independent frameworks.
- **On-demand loading**: tutorials, commands, cases, and software details live in `references/` or `evals/`; `references/manifest.md` provides the loading index.
- **Single source of truth**: volatile info already covered by external Skills or official docs is not re-maintained here.
- **Risk-aware**: dangerous operations require verifying targets and recovery paths first; validation scales with task risk.
- **Evolvable**: rules are Git-managed, but historical states and command details stay out of the behavioral entry point.

## Directory Structure

```text
Agent Rules/
├── AGENTS.md              # Main entry: activation, routing, general behavior, risk boundaries
├── 数学工作者.md          # Math derivation, proof, validation, knowledge capture
├── Latex排版助手.md       # LaTeX projects, safe editing, compilation, acceptance
├── Calibre书库管理.md     # E-book library, metadata, batch writing
├── Coder.md               # Coding and Codex capability routing
├── GitHub操作手册.md      # Git/GitHub maintenance workflows for this rule base
├── references/            # Stable methods and domain details read on demand
│   ├── manifest.md        # Loading index: resident vs on-demand reference manifest
│   ├── math/              # Math sub-references (rigor/workflows/numerics, etc.)
│   └── latex/             # LaTeX sub-references (compile/typography/safe-editing, etc.)
└── evals/                 # Routing, boundary, and domain behavior regression cases
```

## Usage

1. After the user explicitly points at this directory or `AGENTS.md`, read the main entry first; sections 3 and 4 of `AGENTS.md` are resident discipline and apply to any matched task.
2. Pick one main framework by the task's core correctness trigger pointer; mixed tasks load auxiliary frameworks only for their scoped sub-tasks.
3. Use `references/manifest.md` to locate and read only the references relevant to the current sub-task.
4. Report results, uncertainties, and residual risk against the framework's minimum acceptance standard.

## Regression Verification

After changing the routing table, trigger pointers, or any domain framework's safety/acceptance boundary, re-run the corresponding `evals/` cases (routing: `routing-cases.md`; safety: `safety-cases.md`) to ensure behavior has not drifted.

## Relationship with the Skills Branch

The four domain Skills (math/latex/coder/calibre) live on the main branch `skill-trae`; this branch focuses on versioned management of the rule base itself. Both sides share the same domain boundaries and acceptance standards, but their content does not interfere.
