# Agent Rules Skills — Four Domain Skills (Main Branch)

**Language:** [English](README.md) | [中文](README.zh-CN.md)

This branch hosts the four split, independently loadable Skills, each auto-triggered by the `description` in its `SKILL.md`. The rule-base body (Markdown rules) is maintained separately on the `agent-rules-md` branch; the two branches do not interfere with each other.

## Directory Structure

```text
skill-trae/
├── skills/
│   ├── math/              # Math derivation, verification, proofs, notes
│   │   ├── SKILL.md
│   │   └── references/optional/   # rigor / pde-galerkin / notes
│   ├── latex/             # LaTeX compilation, typesetting, safe editing
│   │   ├── SKILL.md
│   │   └── references/optional/   # compile / typography / beamer
│   ├── coder/             # Coding, review, Skill/plugin development
│   │   ├── SKILL.md
│   │   ├── references/optional/review.md
│   │   └── GitHub操作手册.md
│   └── calibre/           # E-book library, metadata, batch writing
│       └── SKILL.md
└── LICENSE                # MIT
```

## Usage

1. After installing into the Skill directory (locally `~/.trae-cn/skills/`), Trae auto-triggers a Skill when a task matches its `SKILL.md` description.
2. When a domain is matched, load its `SKILL.md` and read the relevant `references/optional/` sub-disciplines as needed.
3. Trigger-pointer precision: a single task description should match one and only one domain Skill; adjacent cases are resolved by core correctness risk.

## License

MIT License — see [LICENSE](LICENSE).

## Relationship with the Rule Base (`agent-rules-md`)

The four Skills and the `agent-rules-md` branch share the same domain boundaries and acceptance standards. The Skills carry reusable discipline; the rule base is responsible for routing and boundaries. Content is maintained independently, without duplication.
