# Agent Skills

面向 AI 代理的技能库集合，每项技能以独立目录组织，供 Claude 等助手按需加载。

## 目录结构

```
agent-skills/
├── CLAUDE.md                # Claude 专用项目文档
├── README.md                # 项目说明（开发者向）
└── skills/
    ├── formily/
    │   ├── SKILL.md
    │   └── references/
    │       ├── initial.md
    │       ├── mounting.md
    │       ├── linkage.md
    │       ├── validation.md
    │       ├── layout.md
    │       └── comprehensive-example.md
    └── ...
```

## 技能包规范

每个技能包包含：
- **`SKILL.md`** — 元数据：名称、描述、触发条件、约束、快速参考
- **`references/`** — 按场景拆分的 Markdown 示例（含可复制代码）

## 使用方式

Claude 根据用户问题匹配 `SKILL.md` 中的 When to Apply 条件，加载对应技能。

## 当前技能

| 技能 | 路径 |
|------|------|
| formily | `skills/formily/` |

## 本地调试

加载 `skills/` 下任意技能包即可。