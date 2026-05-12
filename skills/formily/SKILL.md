---
name: 'formily'
description: 'Formily 开发指南：包含实例初始化、组件挂载、表单联动及校验、表单布局的最佳实践。在用户咨询 Formily 相关问题、生成或者修改 Formily 代码时调用。'
---

# Formily Skills

本 Skill 提供了 Formily 表单开发的最佳实践，涵盖实例创建、组件挂载、联动逻辑及表单校验。

## When to Apply

当用户咨询以下内容时调用：

- Formily 相关问题
- 在要求帮忙生成 Formily 最佳实践
- 在要求帮忙生成 Formily 代码
- 初始化 Formily 表单实例
- 挂载 SchemaField 及组件选择
- 实现表单字段间的联动（主动/被动）
- 进行表单校验
- 进行表单布局

## Constraints & Anti-Patterns

- **优先检查依赖**: 生成代码前，**务必**检查 `package.json` 以确认使用 `@formily/antd`。
- **禁止使用 JSON 对象式联动**: 严禁生成 JSON 对象形式的 `x-reactions`，以保证代码的可读性和可维护性。

## Quick Reference

| 场景           | 参考文档                                        | 描述                                                             |
| -------------- | ----------------------------------------------- | ---------------------------------------------------------------- |
| **综合示例**   | [综合示例](references/comprehensive-example.md) | 完整表单示例，包含 TS 类型、布局、联动、校验全流程               |
| **实例初始化** | [相关实例初始化](references/initial.md)         | 包含 createForm, createSchemaField, observable 及组件库选择      |
| **组件挂载**   | [组件挂载](references/mounting.md)              | FormProvider, SchemaField Markup 模式及纯 JSX 注意事项           |
| **表单联动**   | [表单联动](references/linkage.md)               | 推荐函数式 x-reactions，复杂场景使用 effects，禁止使用对象式联动 |
| **表单校验**   | [表单校验](references/validation.md)            | Schema 校验属性、自定义 validator 及手动触发校验                 |
| **表单布局**   | [表单布局](references/layout.md)                | 使用 FormLayout/FormGrid 进行布局                                |
