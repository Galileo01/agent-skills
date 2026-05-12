# Claude 项目说明

本项目是一个技能库，每个技能通过 `SKILL.md` 定义触发条件，Claude 在匹配用户问题时自动加载。

## 当前技能

| 名称 | 描述 | SKILL.md 路径 |
|------|------|--------------|
| formily | Formily 表单开发：实例初始化、组件挂载、联动、校验、布局 | `skills/formily/SKILL.md` |

## 使用示例

用户问："如何在 Formily 中实现字段联动？"
→ 匹配 formily 技能 → 引用 `references/linkage.md` 中的函数式 x-reactions 示例

## 快速索引

- formily 实例初始化 → `skills/formily/references/initial.md`
- formily 组件挂载 → `skills/formily/references/mounting.md`
- formily 表单联动 → `skills/formily/references/linkage.md`
- formily 表单校验 → `skills/formily/references/validation.md`
- formily 表单布局 → `skills/formily/references/layout.md`
- formily 综合示例 → `skills/formily/references/comprehensive-example.md`