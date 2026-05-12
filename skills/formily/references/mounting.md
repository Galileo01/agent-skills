# 组件挂载

## FormProvider

推荐使用 `FormProvider` 组件包裹表单区域，将 `form` 实例向下传递，确保子组件能获取到正确的上下文。

## SchemaField

`SchemaField` 支持 Markup Schema 和 JSON Schema 两种模式。

**在生成代码时，请严格遵循以下决策逻辑：**

1. **默认首选：Markup Schema 模式**
    除非满足下述特定条件，否则**一律**使用 Markup Schema 模式。这种模式代码结构清晰，利于维护，是 React 场景下的最佳实践。

2. **例外情况：JSON Schema 模式**
    仅在以下特定场景下使用 JSON Schema 模式：
    - **用户明确要求**：用户直接指定使用 JSON Schema。
    - **动态表单场景**：用户明确提及“后端接口下发表单配置”、“静态 JSON 渲染表单”、“动态表单”或“低代码”等场景。

### Markup Schema 模式 (默认推荐)

优先使用 Markup 形式的 SchemaField 标签形式定义表单结构。

```tsx
import * as React  from 'react';
import { createForm } from '@formily/core';
import { createSchemaField, FormProvider } from '@formily/react';
import { FormItem, Input } from '@formily/antd';

// SchemaField 在组件外部创建
const SchemaField = createSchemaField({
  components: {
    FormItem,
    Input,
  },
});

const MyPage = () => {
  const form = React.useMemo(() => createForm(), []);

  return (
    <FormProvider form={form}>
      <SchemaField>
        <SchemaField.String
          name="username"
          title="用户名"
          x-decorator="FormItem"
          x-component="Input"
          required
        />
      </SchemaField>
    </FormProvider>
  );
};
```

### JSON Schema 模式 (特定场景)

直接将 JSON 对象传给 `SchemaField` 的 `schema` 属性。

**适用场景**：

- 后端接口直接下发静态 JSON 描述表单结构。
- 需要实现动态化表单方案（如低代码平台）。
- 用户明确要求使用 JSON Schema。

满足以上适用场景时，才使用 JSON Schema 模式：

```tsx
import * as React  from 'react';
import { createForm } from '@formily/core';
import { createSchemaField, FormProvider } from '@formily/react';
import { FormItem, Input } from '@formily/antd';

// SchemaField 在组件外部创建
const SchemaField = createSchemaField({
  components: {
    FormItem,
    Input,
  },
});

// 定义 JSON Schema
const schema = {
  type: 'object',
  properties: {
    username: {
      type: 'string',
      title: '用户名',
      'x-decorator': 'FormItem',
      'x-component': 'Input',
      required: true,
    },
  },
};

const MyPage = () => {
  const form = React.useMemo(() => createForm(), []);

  return (
    <FormProvider form={form}>
      {/* 传入 schema 属性 */}
      <SchemaField schema={schema} />
    </FormProvider>
  );
};
```

## 纯 JSX 书写注意事项

**注意：除非用户明确要求使用纯 JSX 组件形式（如 `<Field>`），否则请优先使用上述的 Markup Schema 模式。**

借助 JSX 直接使用 Field、ArrayField、ObjectField 等书写组件：

- 需手动处理状态绑定（使用 `observer` 在响应式数据变化后 re-render 或 `connect` `mapReadPretty` 定义只读状态下的渲染逻辑）。
- 需手动处理 Field 的路径 (`name`) 和层级。
- 相比 Schema 模式，更加灵活，适合设计复杂 UI 以及需要自定义组件的场景。
